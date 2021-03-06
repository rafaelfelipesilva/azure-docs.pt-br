---
author: WenJason
ms.author: digimobile
ms.service: cognitive-services
ms.topic: include
origin.date: 01/02/2019
ms.date: 01/28/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60327417"
---
As configurações do contêiner são hierárquicas e todos os contêineres no computador host usam uma hierarquia compartilhada.

Você pode usar uma das seguintes opções para especificar as configurações:

* [Variáveis de ambiente](#environment-variable-settings)
* [Argumentos de linha de comando](#command-line-argument-settings)

Os valores de variáveis de ambiente substituem os valores do argumento da linha de comandos, os quais, por sua vez, substituem os valores padrão da imagem do contêiner. Se você especificar valores diferentes em uma variável de ambiente e um argumento de linha de comando para a mesma configuração, o valor na variável de ambiente será usado pelo contêiner instanciado.

|Precedência|Local da configuração|
|--|--|
|1|Variável de ambiente| 
|2|Linha de comando|
|3|Valor padrão de imagem de contêiner|

### <a name="environment-variable-settings"></a>Configurações da variável de ambiente

Os benefícios de se usar variáveis de ambiente são:

* Várias configurações podem ser definidas.
* Vários contêineres podem usar as mesmas configurações.

### <a name="command-line-argument-settings"></a>Configurações do argumento de linha de comando

A vantagem de se usar argumentos de linha de comando é que cada contêiner pode usar configurações diferentes.
