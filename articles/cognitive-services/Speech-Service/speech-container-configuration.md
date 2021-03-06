---
title: Configurar os contêineres de fala
titleSuffix: Azure Cognitive Services
description: O contêiner de fala
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 424396c7b92fb58993c74e672c4c3ea9c8f814c7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026216"
---
# <a name="configure-speech-service-containers"></a>Configurar os contêineres do serviço de fala

Contêineres de fala permitem que os clientes criar uma arquitetura de aplicativos de fala que é otimizada para tirar proveito dos recursos de nuvem robusto e localidade de borda. Os contêineres de dois fala damos suporte agora estão **fala em texto** e **texto em fala**. 

O **fala** ambiente de tempo de execução do contêiner é configurado usando o `docker run` argumentos do comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

# <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o _fala_ recurso especificado para o [ `Billing` ](#billing-configuration-setting) definição de configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Da conversão de fala** gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

O `Billing` configuração especifica o URI do ponto de extremidade do _fala_ recursos no Azure é usado para monitorar informações de cobrança para o contêiner. Você deve especificar um valor para este parâmetro de configuração e o valor deve ser um URI de ponto de extremidade válido para um _fala_ recursos no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Do conversão de fala** visão geral, rotulado `Endpoint`

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Configurações de registro em log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres de fala não usam a entrada ou saída monta para armazenar dados de serviço ou treinamento. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, o local de montagem do [computador host](speech-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões do local de montagem do host. 

|Opcional| NOME | Tipo de dados | DESCRIÇÃO |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Contêineres de fala não usam isso.|
|Opcional| `Output` | String | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](speech-container-howto.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Placeholder | Value | Formato ou exemplo |
|-------------|-------|---|
|{BILLING_KEY} | A chave do ponto de extremidade do recurso de fala. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | O valor de ponto de extremidade cobrança, incluindo a região.|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing-configuration-setting).
> O valor de ApiKey é o **chave** da página de chaves do recurso de fala do Azure. 

## <a name="speech-container-docker-examples"></a>Exemplos de Docker de contêiner de fala

Os exemplos de Docker a seguir são para o contêiner de fala. 

### <a name="basic-example-for-speech-to-text"></a>Exemplo básico de fala em texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
```

### <a name="basic-example-for-text-to-speech"></a>Exemplo básico de texto em fala

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
```

### <a name="logging-example-for-speech-to-text"></a>Exemplo de registro em log de conversão de fala para texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
  Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Exemplo de registro em log de texto em fala

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
  Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](speech-container-howto.md)