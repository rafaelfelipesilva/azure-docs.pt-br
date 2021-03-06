---
title: Configurar o tempo de execução de PHP – Serviço de Aplicativo do Azure
description: Saiba como configurar a instalação padrão do PHP ou adicionar uma instalação de PHP personalizada no Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: eb731dc18b1524bcf161352265af9e277f85876e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730627"
---
# <a name="configure-php-in-azure-app-service"></a>Configurar o PHP no Serviço de Aplicativo do Azure

## <a name="introduction"></a>Introdução

Este guia mostra como configurar o tempo de execução do PHP interno para aplicativos Web, back-ends móveis e aplicativos de API no [Serviço de Aplicativo do Azure](https://go.microsoft.com/fwlink/?LinkId=529714), fornecer um tempo de execução do PHP personalizado e habilitar extensões. Para usar o Serviço de Aplicativo, inscreva-se para a [avaliação gratuita]. Para aproveitar ao máximo este guia, você deve primeiro criar um aplicativo PHP no Serviço de Aplicativo.

## <a name="how-to-change-the-built-in-php-version"></a>Como: Alterar a versão interna do PHP

Por padrão, o PHP 5.6 é instalado e fica imediatamente disponível para uso quando você cria um aplicativo do Serviço de Aplicativo. A melhor forma de visualizar a revisão da versão, sua configuração padrão e as extensões habilitadas é implantar um script que chame a função [phpinfo()] .

As versões 7.0 e 7.2 do PHP também estão disponíveis, mas não são habilitadas por padrão. Para atualizar a versão do PHP, execute um destes métodos:

### <a name="azure-portal"></a>Portal do Azure

1. Navegue até seu aplicativo na [portal do Azure](https://portal.azure.com) e role até a **configuração** página.

2. Partir **Configuration**, selecione **configurações gerais** e escolha a nova versão do PHP.

3. Clique o **salve** botão na parte superior do **configurações gerais** folha.

### <a name="azure-powershell-windows"></a>PowerShell do Azure (Windows)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Abra o Azure PowerShell e faça logon em sua conta:

        PS C:\> Connect-AzAccount
2. Defina a versão PHP do aplicativo.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
3. A versão do PHP agora está definida. Você pode confirmar essas configurações:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>CLI do Azure 

Para usar a Interface de Linha de Comando do Azure, você deve [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em seu computador.

1. Abra o Terminal e faça logon em sua conta.

        az login

1. Verifique para ver a lista de tempos de execução com suporte.

        az webapp list-runtimes | grep php

2. Defina a versão PHP do aplicativo.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. A versão do PHP agora está definida. Você pode confirmar essas configurações:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como: Alterar as configurações internas do PHP

Para qualquer tempo de execução interno do PHP, é possível alterar qualquer uma das opções de configuração seguindo estas etapas. (Para obter informações sobre diretrizes de php. ini, consulte [Lista de diretrizes php.ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Alterando as configurações de PHP\_INI\_USER, PHP\_INI\_PERDIR e PHP\_INI\_ALL

1. Adicione um arquivo [.user.ini] no seu diretório raiz.
1. Adicione as definições de configuração ao arquivo `.user.ini` usando a mesma sintaxe que você usaria em um arquivo `php.ini`. Por exemplo, se você quisesse ativar a configuração `display_errors` e definir a configuração `upload_max_filesize` para 10M, o arquivo `.user.ini` conteria este texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implante seu aplicativo.
3. Reinicie o aplicativo. (É necessário reiniciar, pois a frequência com a qual o PHP lê arquivos `.user.ini` é regida pela configuração`user_ini.cache_ttl`, que é uma configuração de nível de sistema e é, por padrão, 300 segundos (5 minutos). Reiniciar o aplicativo força o PHP a ler as novas configurações no arquivo `.user.ini`.)

Uma alternativa ao uso de um arquivo `.user.ini` é usar a função [ini_set()] em scripts para definir opções de configuração que não sejam diretrizes de nível de sistema.

### <a name="changing-phpinisystem-configuration-settings"></a>Alterando as configurações de PHP\_INI\_SYSTEM

1. Adicionar uma Configuração de Aplicativo ao seu aplicativo com a chave `PHP_INI_SCAN_DIR` e o valor `d:\home\site\ini`
1. Crie um arquivo `settings.ini` usando o console Kudu (http://&lt;nome-do-site&gt;.scm.azurewebsite.net) no diretório `d:\home\site\ini`.
1. Adicione as definições de configuração ao arquivo `settings.ini` usando a mesma sintaxe que você usaria em um arquivo `php.ini`. Por exemplo, se você quisesse apontar a configuração `curl.cainfo` para um arquivo `*.crt` e definir a configuração 'wincache.maxfilesize' como 512 K, o arquivo `settings.ini` conteria este texto:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Para recarregar as alterações, reinicie seu aplicativo.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como: Habilitar extensões no tempo de execução padrão do PHP

Conforme indicado na seção anterior, a melhor forma de visualizar a versão padrão do PHP, sua configuração padrão e as extensões habilitadas é implantar um script que chame [phpinfo()]. Para habilitar extensões adicionais, siga estas etapas:

### <a name="configure-via-ini-settings"></a>Configurar por meio de configurações ini

1. Adicione um diretório `ext` ao diretório `d:\home\site`.
1. Coloque arquivos de extensão `.dll` no diretório `ext` (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões sejam compatíveis com a versão padrão do PHP e também com nts (non-thread-safe) e VC9.
1. Adicionar uma Configuração de Aplicativo ao seu aplicativo com a chave `PHP_INI_SCAN_DIR` e o valor `d:\home\site\ini`
1. Crie um arquivo `ini` em `d:\home\site\ini` chamado `extensions.ini`.
1. Adicione as definições de configuração ao arquivo `extensions.ini` usando a mesma sintaxe que você usaria em um arquivo `php.ini`. Por exemplo, se você desejasse habilitar as extensões MongoDB e XDebug, o arquivo `extensions.ini` conteria este texto:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Reinicie seu aplicativo para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configurar por meio de Configuração de Aplicativo

1. Adicione um diretório `bin` ao diretório raiz.
2. Coloque arquivos de extensão `.dll` no diretório `bin` (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões sejam compatíveis com a versão padrão do PHP e também com nts (non-thread-safe) e VC9.
3. Implante seu aplicativo.
4. Navegue até o aplicativo no portal do Azure e clique no **Configuration** localizada abaixo **configurações** seção.
5. Dos **Configuration** folha, selecione **configurações do aplicativo**.
6. No **configurações do aplicativo** seção, clique em **+ a nova configuração de aplicativo** e crie um **PHP_EXTENSIONS** chave. O valor para essa chave seria um caminho relativo à raiz do site: **bin\seu-arquivo-de-ext**.
7. Clique no **atualização** botão na parte inferior e clique em **salve** acima a **configurações de aplicativo** guia.

Também há suporte para extensões Zend usando uma chave **PHP_ZENDEXTENSIONS**. Para habilitar várias extensões, inclua uma lista de arquivos `.dll` separados por vírgulas para o valor de configuração do aplicativo.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: Usar um tempo de execução personalizado do PHP

Em vez do tempo de execução padrão do PHP, o Serviço de Aplicativo pode usar um tempo de execução de PHP fornecido por você para executar scripts PHP. O tempo de execução que você fornece pode ser configurado por um arquivo `php.ini` também fornecido por você. Para usar um tempo de execução do PHP personalizado com Serviço de Aplicativo, siga estas etapas.

1. Obtenha uma versão do PHP ou VC11 para Windows que seja não thread safe e compatível com a versão VC9. Versões recentes do PHP para Windows podem ser encontradas aqui: [https://windows.php.net/download/]. Versões mais antigas podem ser encontradas no arquivo aqui: [https://windows.php.net/downloads/releases/archives/].
2. Modifique o arquivo `php.ini` para o seu tempo de execução. Quaisquer definições de configuração que sejam diretivas somente de nível do sistema são ignoradas pelo Serviço de Aplicativo. (Para informações sobre diretrizes de nível de sistema apenas, consulte [Lista de diretrizes php.ini].)
3. Opcionalmente, adicione extensões para o seu tempo de execução do PHP e habilite-as no arquivo `php.ini` .
4. Adicione o diretório `bin` ao seu diretório raiz e coloque lá o diretório que contém o seu tempo de execução do PHP (por exemplo, `bin\php`).
5. Implante seu aplicativo.
6. Navegue até o aplicativo no portal do Azure e clique no **configuração** folha.
8. Dos **Configuration** folha, selecione **mapeamentos de caminho**. 
9. Clique em **+ novo manipulador** e adicione `*.php` ao campo extensão e adicione o caminho para o `php-cgi.exe` executável na **processador de Script**. Se você colocar o tempo de execução do PHP no diretório `bin` na raiz do aplicativo, o caminho será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Na parte inferior, clique em **atualização** para terminar de adicionar o mapeamento de manipulador.
11. Clique em **Salvar** para salvar as alterações.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Como: Habilitar a automação do Criador no Azure

Por padrão, o Serviço de Aplicativo não fará nada com o composer.json se você tiver um em seu projeto PHP. Se você usar a [implantação Git](deploy-local-git.md), você poderá habilitar o composer.json durante o `git push` habilitando a extensão do Criador.

> [!NOTE]
> Você pode [votar para obter o suporte de primeira classe do Criador no Serviço de Aplicativo aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip).
>

1. Na folha do aplicativo PHP no [portal do Azure](https://portal.azure.com), clique em **Ferramentas** > **Extensões**.

    ![Folha de configurações do Portal do Azure para habilitar a automação do Criador no Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique em **Adicionar** e em **Criador**.

    ![Adicionar extensão do Criador para habilitar sua respectiva automação no Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique em **OK** para aceitar os termos legais. Clique em **OK** novamente para adicionar a extensão.

    Agora, a folha **Extensões instaladas** mostra a extensão do Criador.
    ![Aceite os termos legais para habilitar a automação do Criador no Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, em uma janela do terminal no computador local, execute `git add`, `git commit` e `git push` no seu aplicativo. Observe que o Criador está instalando as dependências definidas no composer.json.

    ![Implantação do Git com a automação do Criador no Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](https://azure.microsoft.com/develop/php/).

[avaliação gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de diretrizes php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
