---
title: Limitar o acesso a kubeconfig no AKS (Serviço de Kubernetes do Azure)
description: Aprenda a controlar o acesso ao arquivo de configuração Kubernetes (kubeconfig) para os administradores de cluster e os usuários de cluster
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 01/03/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 141aacc71d129bb45dc53774af876d5b07b7fc86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466437"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Usar os controles de acesso baseado em função do Azure para definir o acesso ao arquivo de configuração do Kubernetes no AKS (Serviço de Kubernetes do Azure)

Você pode interagir com um cluster Kubernetes usando a ferramenta `kubectl`. A CLI do Azure fornece uma maneira fácil de obter as credenciais de acesso e informações de configuração para se conectar aos seus clusters do AKS usando o `kubectl`. Para limitar quem pode obter essas informações de configuração do Kubernetes (*kubeconfig*) e para limitar as permissões que essas pessoas têm, você pode usar RBACs (controles de acesso baseado em função) do Azure.

Este artigo mostra como atribuir funções RBAC que limitam quem pode obter as informações de configuração para um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Este artigo exige que você esteja executando a versão 2.0.53 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Permissões de funções de cluster disponíveis

Quando você interage com um cluster do AKS usando a ferramenta `kubectl`, é usado um arquivo de configuração que define as informações de conexão do cluster. Esse arquivo de configuração normalmente é armazenado em *~/.kube/config*. Vários clusters podem ser definidos nesse arquivo *kubeconfig*. Você pode alternar entre clusters usando o comando [kubectl config use-context][kubectl-config-use-context].

O comando [az aks get-credentials][az-aks-get-credentials] permite que você obtenha as credenciais de acesso para um cluster do AKS e mescle-os ao arquivo *kubeconfig*. Você pode usar RBACs (controles de acesso baseado em função) do Azure para controlar o acesso a essas credenciais. Essas funções de RBAC do Azure permitem que você defina quem pode recuperar o arquivo *kubeconfig* e, se alguém o fizer, quais permissões esse alguém terá dentro do cluster.

As duas funções internas são:

* **Função de Administrador do Cluster do Serviço de Kubernetes do Azure**  
    * Permite acesso à chamada à API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Essa chamada à API [lista as credenciais de administrador do cluster][api-cluster-admin].
    * Baixa o *kubeconfig* para a função *clusterAdmin*.
* **Função de Usuário do Cluster do Serviço de Kubernetes do Azure**
    * Permite acesso à chamada à API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Essa chamada à API [lista as credenciais de usuário do cluster][api-cluster-user].
    * Baixa *kubeconfig* para a função *clusterUser*.

## <a name="assign-role-permissions-to-a-user"></a>Atribuir permissões de função a um usuário

Para atribuir uma das funções do Azure a um usuário, você precisa obter a ID do recurso de cluster do AKS e a ID da conta de usuário. Os seguintes comandos de exemplo realizam as seguintes etapas:

* Obtêm a ID de recurso de cluster usando o comando [az aks show][az-aks-show] para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup*. Fornecem seu próprio cluster e nome de grupo de recursos, conforme necessário.
* Usam os comandos [az account show][az-account-show] e [az ad user show][az-ad-user-show] para obter sua ID de usuário.
* Por fim, atribuem uma função usando o comando [az role assignment create][az-role-assignment-create].

O exemplo a seguir atribui a *Função de Administrador do Cluster do Serviço de Kubernetes do Azure*:

```azurecli
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

Você pode alterar a atribuição anterior para a *função de usuário de cluster* conforme necessário.

A saída de exemplo a seguir mostra que a atribuição de função foi criada com êxito:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Obter e verificar as informações de configuração

Com as funções RBAC atribuídas, use o comando [az aks get-credentials][az-aks-get-credentials] para obter a definição *kubeconfig* para o cluster do AKS. O exemplo a seguir obtém as credenciais *-- admin*, que funcionarão corretamente se a *função de administrador do cluster* tiver sido concedida ao usuário:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, você pode usar o comando [kubectl config view][kubectl-config-view] para verificar se o *contexto* do cluster mostra que as informações de configuração do administrador foram aplicadas:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.chinaeast.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Remover permissões de função

Para remover uma atribuição de função, use o comando [az role assignment delete][az-role-assignment-delete]. Especifique a ID da conta e a ID de recurso de cluster, conforme obtidas nos comandos anteriores:

```azurecli
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Próximos passos

Para segurança aprimorada no acesso aos clusters do AKS, [integre a autenticação do Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: https://docs.microsoft.com/rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: https://docs.microsoft.com/rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[az-account-show]: https://docs.azure.cn/zh-cn/cli/account?view=azure-cli-latest#az-account-show
[az-ad-user-show]: https://docs.azure.cn/zh-cn/cli/ad/user?view=azure-cli-latest#az-ad-user-show
[az-role-assignment-create]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-create
[az-role-assignment-delete]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-delete
[aad-integration]: aad-integration.md