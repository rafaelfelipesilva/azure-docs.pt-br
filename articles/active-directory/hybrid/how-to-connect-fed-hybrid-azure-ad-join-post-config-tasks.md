---
title: 'Azure AD Connect: Tarefas de configuração de postagem conjunta do Microsoft Azure Active Directory Híbrido| Microsoft Docs'
description: Este documento detalha as tarefas de publicação necessárias para concluir a junção do Microsoft Azure Active Directory Híbrido
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9af969700f4f2dfbedc4833badd7e7349696302
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60244586"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Tarefas de configuração de postagem para junção do Microsoft Azure Active Directory Híbrido

Depois que você executou a conexão do Azure AD Connect para configurar sua organização para junção do o Microsoft Azure Active Directory Híbrido há algumas etapas adicionais que você deve concluir para finalizar a instalação.  Execute apenas as etapas que se aplicam a seus dispositivos.

## <a name="1-configure-controlled-rollout-optional"></a>1. Configurar distribuição controlada (opcional)
Todos os dispositivos associados ao domínio executando o Windows 10 e o Windows Server 2016 serão registrados automaticamente no Azure AD uma vez que todas as etapas de configuração foram concluídas. Se você preferir um desenvolvimento controlado em vez do registro automático, você pode usar a diretiva de grupo para seletivamente habilitar ou desabilitar a distribuição automática.  Essa política de grupo deve ser definida antes de iniciar outras etapas de configuração: Azure AD
* Crie um objeto de diretiva de grupo no seu Active Directory.
* Coloque um nome (por exemplo, junção do Azure AD Híbrido).
* Editar e Ir para:  Configuração do Computador > Políticas > Modelos Administrativos > Componentes do Windows > Registro de Dispositivos.

>[!NOTE]
>Para 2012R2 as configurações da política estão em **Configuração do computador > Políticas > Modelos Administrativos > Componentes do Windows > Ingresso no Local de Trabalho > Ingressar computadores cliente no local de trabalho automaticamente**

* Desabilite essa configuração:  Registrar computadores ingressados no domínio como dispositivos.
* Aplique e clique em OK.
* Vincule o GPO para o local de sua escolha (unidade organizacional, grupo de segurança, ou para o domínio para todos os dispositivos).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Configurar a rede com pontos de extremidade de registro de dispositivo
Certifique-se de que as seguintes URLs são acessíveis a partir de computadores em rede da sua organização para o registro de no Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementar dispositivos WPAD para dispositivos do Windows 10
Se sua organização acessar Internet através de um proxy de saída, implemente a Descoberta Automática de Proxy da Web (WPAD) para permitir que computadores com Windows 10 registrem-se no Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Configurar o SCP em todas as florestas que não foram configuradas pelo Azure AD Connect 

O objeto de ponto de conexão de serviço (SCP) é usado por seus dispositivos durante o registro para descobrir informações de locatário do Azure AD.  Execute o script do PowerShell, ConfigureSCP.ps1, que você baixou do Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Configurar o SCP em todas as florestas que não foram configuradas pelo Azure AD Connect

Se a sua organização usa um serviço de federação para entrar no Azure AD, as regras de declaração de terceira parte confiável seu Azure AD devem permitir a autenticação de dispositivo. Se você estiver usando a federação com o AD FS, vá para [Ajuda do AD FS](https://aka.ms/aadrptclaimrules) para gerar as regras de declaração. Se você estiver usando uma solução de Federação não Microsoft, entre em contato com o provedor para obter orientação.  

>[!NOTE]
>Se você tiver dispositivos de nível inferior do Windows, o serviço deve dar suporte a emissão de declarações authenticationmethod e wiaormultiauthn durante o recebimento de solicitações para a relação de confiança do Microsft Azure Active Directory. No AD FS, você precisa adicionar uma regra de transformação de emissão que passe pelo método de autenticação.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Ativar SSO contínuo do Microsoft Azure Active Directory para dispositivos de nível Windows

Se sua organização usa a autenticação de passagem ou sincronização de Hash de senha para entrar no Microsoft Azure Active Directory, habilite o SSO contínuo do Microsoft Azure Active Directory com esse método de entrada para autenticar dispositivos de baixo nível do Windows: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Definir a política do Microsoft Azure Active Directory para dispositivos nível inferior do Windows

Para registrar dispositivos de nível inferior do Windows, você precisa garantir que a política do Microsoft Azure Active Directory permita que todos os usuários registrem os dispositivos. 

* Efetue logon na sua conta no portal do Azure.
* Acesse:  Microsoft Azure Active Directory > Dispositivos > Configurações do dispositivo
* Defina “Usuários podem registrar seus dispositivos com o Microsoft Azure Active Directory” para TODOS. 
* Clique em Salvar

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Adicionar o ponto de extremidade do Microsoft Azure Active Directory para os dispositivos de nível inferior do Windows

Adicionar o ponto de extremidade de autenticação de dispositivo do Microsoft Azure Active Directory às zonas da Intranet local nos seus dispositivos de nível inferior do Windows para evitar prompts de cerificado ao autenticar os dispositivos: https://device.login.microsoftonline.com 

Se você estiver usando [SSO contínuo](how-to-connect-sso.md), habilite também "Permitir atualizações da barra de status por meio de script" na zona e adicione o ponto de extremidade a seguir: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instalar o Microsoft Workplace Join em dispositivos de baixo nível do Windows

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo no Azure AD com as credenciais do usuário após a autenticação usando a Autenticação Integrada do Windows. O centro de download ESTÁ EM https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Configurar a política de grupo para permitir o registro de dispositivo

* Crie um objeto de diretiva de grupo no seu Active Directory - se ainda não tiver criado.
* Coloque um nome (por exemplo, junção do Azure AD Híbrido).
* Editar e Ir para:  Configuração do Computador > Políticas > Modelos Administrativos > Componentes do Windows > Registro de Dispositivos
* Habilitar:  Registrar computadores ingressados no domínio como dispositivos
* Aplique e clique em OK.
* Vincule o GPO para o local de sua escolha (unidade organizacional, grupo de segurança, ou para o domínio para todos os dispositivos).

>[!NOTE]
>Para 2012R2 as configurações da política estão em **Configuração do computador > Políticas > Modelos Administrativos > Componentes do Windows > Ingresso no Local de Trabalho > Ingressar computadores cliente no local de trabalho automaticamente**

## <a name="next-steps"></a>Próximas etapas
[Configurar o write-back de dispositivo](how-to-connect-device-writeback.md)
