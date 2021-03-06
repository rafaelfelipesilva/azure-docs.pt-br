---
title: Copiar ou mover dados para o armazenamento do Azure usando o AzCopy v10 | Microsoft Docs
description: Use o utilitário de linha de comando AzCopy para mover ou copiar dados para ou de BLOBs, data lake e o conteúdo do arquivo. Copie dados para o Armazenamento do Azure de arquivos locais ou copie dados dentro na mesma conta ou entre contas de armazenamento. Migre facilmente seus dados para o Armazenamento do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: d05cbd30565ced73352736508fc4bcc376985554
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149029"
---
# <a name="transfer-data-with-azcopy-v10"></a>Transferir dados com AzCopy v10

O AzCopy é o utilitário de linha de comando para copiar dados do armazenamento de BLOBs do Microsoft Azure e o arquivo. AzCopy oferece uma interface de linha de comando redesenhada e transfere a nova arquitetura de dados confiável. Usando o AzCopy, você pode copiar dados entre um sistema de arquivos e uma conta de armazenamento, ou entre contas de armazenamento.

## <a name="whats-new-in-azcopy-v10"></a>O que há de novo no AzCopy v10

- Sincroniza os sistemas de arquivos para o armazenamento de BLOBs do Azure ou vice-versa. Usar o `azcopy sync <source> <destination>`. Ideal para cenários de cópia incremental.
- Dá suporte a APIs do Azure Data Lake Storage Gen2. Use `myaccount.dfs.core.windows.net` como um URI para chamar as APIs de Gen2 de armazenamento do Data Lake.
- Dá suporte à cópia de uma conta inteira (apenas serviço Blob) para outra conta.
- Dá suporte à cópia de dados de um bucket S3 do Amazon Web Services.
- Usa o novo [Put Block da URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) APIs para dar suporte à cópia de conta para conta. A transferência de dados é mais rápida, já que a transferência para o cliente não é necessária.
- Lista ou remove arquivos e blobs em um determinado caminho.
- Dá suporte a padrões de curinga em um caminho de e --exclude sinalizadores.
- Cria um pedido de trabalho e um arquivo de log relacionados com cada instância de AzCopy. Você pode exibir e reiniciar trabalhos anteriores e retomar trabalhos com falha. O AzCopy também tentará automaticamente realizar uma nova transferência após uma falha.
- Melhorias de desempenho geral de recursos.

## <a name="download-and-install-azcopy"></a>Baixar e instalar o AzCopy

### <a name="latest-production-version-v10"></a>Versão de produção mais recente (v10)

Baixe a versão mais recente do AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-azcopy-supporting-table-storage-service-v73"></a>AzCopy mais recente oferece suporte ao serviço de armazenamento de tabela (v 7.3)

Baixe o [AzCopy v7.3 que dá à cópia de dados para/do serviço de armazenamento de tabelas do Microsoft Azure](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Etapas pós-instalação

O AzCopy não exige uma instalação. Abra seu aplicativo de linha de comando preferido e navegue até a pasta onde `azcopy.exe` está localizado. Se necessário, você pode adicionar o local da pasta AzCopy ao caminho do sistema para facilidade de uso.

## <a name="authentication-options"></a>Opções de autenticação

O AzCopy suporta as seguintes opções ao autenticar com o armazenamento do Azure:
- **O Azure Active Directory** (suporte para **serviços Blob e Data Lake armazenamento Gen2**). Use ```.\azcopy login``` para entrar com o Azure Active Directory.  O usuário deve ter [função "Colaborador de dados de Blob de armazenamento" atribuída](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) para gravar no armazenamento de Blob com a autenticação do Active Directory do Azure. Para a autenticação por meio de identidades gerenciadas para recursos do Azure, use `azcopy login --identity`.
- **Compartilhado tokens de assinatura de acesso [com suporte para serviços Blob e arquivo]**. Acrescente o token SAS (assinatura) de acesso compartilhado para o caminho de blob na linha de comando para usá-lo. Você pode gerar tokens SAS com o portal do Azure, [Gerenciador de armazenamento](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), ou outras ferramentas de sua escolha. Para obter mais informações, confira [exemplos](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Introdução

> [!TIP]
> **Prefere uma interface gráfica do usuário?**
>
> [O Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), um cliente de desktop que simplifica o gerenciamento de dados de armazenamento do Azure, agora usa o AzCopy para acelerar a transferência de dados de e para fora do armazenamento do Azure.
>
> Habilitar o AzCopy no Gerenciador de armazenamento sob o **visualização** menu.
> ![Habilitar o AzCopy como um mecanismo de transferência no Gerenciador de armazenamento do Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

V10 AzCopy tem uma sintaxe documentada Self. Quando você tiver conectado ao Azure Active Directory, a sintaxe geral é semelhante ao seguinte:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

É assim que você pode obter uma lista dos comandos disponíveis:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Para ver a página de Ajuda e exemplos para um comando específico, execute o seguinte comando:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Criar um compartilhamento de arquivo ou contêiner de blob 

**Criar um contêiner de blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Criar um compartilhamento de arquivos**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Criar um contêiner de blob usando o Azure Data Lake armazenamento Gen2**

Se você tiver habilitado a namespaces hierárquicos em sua conta de armazenamento de BLOBs, você pode usar o comando a seguir para criar um novo contêiner de blob para carregar arquivos.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copiar dados para o Armazenamento do Azure

Use o comando de cópia para transferir dados da origem para o destino. A origem ou destino pode ser:
- Sistema de arquivos local
- URI de Contêiner/Diretório Virtual/Blob do Azure
- URI de Compartilhamento de Arquivo/Diretório/Arquivo do Azure
- Sistema de Arquivos do Azure Data Lake Storage Gen2/Diretório/URI de Arquivo

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

O comando a seguir carrega todos os arquivos na pasta `C:\local\path` recursivamente no contêiner `mycontainer1`, criando `path` diretório no contêiner. Quando `--put-md5` sinalizador for fornecido, o AzCopy calcula e armazena o hash md5 de cada arquivo no `Content-md5` propriedade do blob correspondente para uso posterior.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

O comando a seguir carrega todos os arquivos da pasta `C:\local\path` (sem fazer recursão nos subdiretórios) no contêiner `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

Para obter mais exemplos, use o seguinte comando:

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>Copiar dados entre duas contas de armazenamento de Blob

Copiar dados entre duas contas de armazenamento usa a [colocar bloco From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API e não usa largura de banda de rede do computador cliente. Dados são copiados entre dois servidores de armazenamento do Azure diretamente, enquanto o AzCopy simplesmente orquestra a operação de cópia. Essa opção só está disponível para o armazenamento de BLOBs.

Para copiar todos os dados entre duas contas de armazenamento de Blob, use o seguinte comando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Para copiar um contêiner de Blob para outro contêiner de Blob, use o seguinte comando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copiar uma imagem de VHD para uma conta de armazenamento

Por padrão, o AzCopy carrega dados em blobs de blocos. Para carregar arquivos como Blobs de acréscimo ou Blobs de página, use o sinalizador `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Sincronização: cópia e exclusão incremental (somente armazenamento de Blobs)

O comando de sincronização sincroniza conteúdo de um diretório de origem para um diretório no destino, comparando os nomes de arquivo e modificado pela última vez os carimbos de hora. Essa operação inclui a exclusão opcional dos arquivos de destino se eles não existirem na fonte quando o `--delete-destination=prompt|true` sinalizador é fornecido. Por padrão, o comportamento de exclusão está desabilitado. 

> [!NOTE] 
> Use o `--delete-destination` sinalizador com cuidado. Habilitar o [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) antes de habilitar o comportamento de exclusão em sincronia para impedir exclusões acidentais na sua conta de recurso. 
>
> Quando `--delete-destination` é definido como true, o AzCopy excluirá os arquivos que não existem na fonte de destino sem qualquer solicitação para o usuário. Se você quiser ser solicitado a confirmar, use `--delete-destination=prompt`.

Para sincronizar seu sistema de arquivos local com uma conta de armazenamento, use o seguinte comando:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Você também pode sincronizar um contêiner de blob para baixo para um sistema de arquivos local:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Este comando sincroniza incrementalmente a origem para o destino com base nos carimbos de hora última modificação. Se você adicionar ou excluir um arquivo na fonte, o AzCopy fará o mesmo no destino. Antes da exclusão, o AzCopy solicitará que você confirme.

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>Copiar dados do Amazon Web Services (AWS) S3

Para autenticar com um bucket AWS S3, defina as seguintes variáveis de ambiente:

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

Para copiar o número de buckets para um contêiner de Blob, emita o seguinte comando:

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

Para obter mais detalhes sobre como copiar dados do AWS S3 usando o AzCopy, consulte a página [aqui](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3).

## <a name="advanced-configuration"></a>Configuração avançada

### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Para configurar as configurações de proxy para AzCopy v10, defina o https_proxy de variável de ambiente usando o seguinte comando:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Otimizar a taxa de transferência

Defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE para configurar o número de solicitações simultâneas e para controlar o consumo de recursos e desempenho de taxa de transferência. O valor é definido como 300 por padrão. Reduzir o valor limitará a largura de banda e a CPU usadas pelo AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Alterar a localização dos arquivos de log

Você pode alterar a localização dos arquivos de log, se necessário, ou evitar que o disco do sistema operacional fique cheio.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Alterar o nível de log padrão 

Por padrão, o nível de log de AzCopy é definido como INFO. Se você quiser reduzir o detalhamento do log para economizar espaço em disco, substitua a configuração usando a opção ``--log-level``. Os níveis de log disponíveis são: DEPURAÇÃO, informações, aviso, erro, entre em PANE e FATAL.

### <a name="review-the-logs-for-errors"></a>Examinar os logs em busca de erros

O comando a seguir obtém todos os erros com o status UPLOADFAILED do log 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>solução de problemas

O AzCopy cria arquivos de log e arquivos de plano para cada trabalho. Você pode usar os logs para investigar e solucionar problemas potenciais. Os logs conterão o status de falha (UPLOADFAILED, COPYFAILED e DOWNLOADFAILED), o caminho completo e o motivo da falha. Os logs de trabalho e os arquivos de plano estão localizados na pasta %USERPROFILE\\.azcopy no Windows ou na pasta $HOME\\.azcopy no Mac e Linux.

> [!IMPORTANT]
> Ao enviar uma solicitação para o Microsoft Support (ou solucionar o problema que envolvem qualquer terceiro), compartilhar a versão em uma versão editada do comando que você deseja executar. Isso garante que a SAS não sejam acidentalmente compartilhada com qualquer pessoa. Você pode encontrar a versão editada no início do arquivo de log.

### <a name="view-and-resume-jobs"></a>Exibir e retomar trabalhos

Cada operação de transferência criará um trabalho do AzCopy. Use o seguinte comando para exibir o histórico de trabalhos:

```azcopy
.\azcopy jobs list
```

Para exibir as estatísticas de trabalho, use o seguinte comando:

```azcopy
.\azcopy jobs show <job-id>
```

Para filtrar as transferências por status, use o seguinte comando:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Use o comando a seguir para retomar um trabalho com falha/cancelado. Esse comando usa seu identificador juntamente com o token SAS, pois não é persistente por motivos de segurança:

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas, problemas ou comentários gerais, enviá-los [no GitHub](https://github.com/Azure/azure-storage-azcopy).


