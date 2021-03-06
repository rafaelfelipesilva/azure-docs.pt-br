---
title: Saiba como o gerenciador de segurança protege dispositivos, software – Azure IoT Edge | Microsoft Docs
description: Gerencia a postura de segurança do dispositivo IoT Edge e a integridade dos serviços de segurança.
services: iot-edge
keywords: segurança, o elemento seguro, o enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bc441e2bbd36c8d078eb67aff48e58684a026289
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444953"
---
# <a name="azure-iot-edge-security-manager"></a>Gerenciador de segurança do Azure IoT Edge

O gerenciador de segurança do Azure IoT Edge é um núcleo de segurança bem delimitado para proteger o dispositivo IoT Edge e todos os seus componentes, abstraindo o hardware de silício seguro. É o ponto focal para proteção de segurança e fornece integração de tecnologia para os fabricantes de dispositivos originais (OEM).

![Gerenciador de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

O gerenciador de segurança IoT Edge visa defender a integridade do dispositivo IoT Edge e todas as operações de software inerentes.  Isso é feito através da transição da confiança da raiz de hardware subjacente do hardware de confiança (se disponível) para fazer o bootstrap do tempo de execução do IoT Edge com segurança e continuar a monitorar a integridade de suas operações.  O gerenciador de segurança IoT Edge inclui software que trabalha em conjunto com hardware de silício seguro, quando disponível, para ajudar a fornecer as mais altas garantias de segurança possíveis.  

As responsabilidades do gerenciador de segurança do IoT Edge incluem (mas sem limitação):

* Inicialização segura e medida do dispositivo Azure IoT Edge.
* Fornecimento de identidade de dispositivo e transição de confiança, quando aplicável.
* Hospede e proteja componentes de dispositivos de serviços em nuvem, como o Serviço de provisionamento de dispositivos.
* Forneça com segurança identidades exclusivas aos módulos do IoT Edge.
* Gatekeeper para dispositivo hardware raiz de confiança através de serviços notariais.
* Monitora a integridade das operações do IoT Edge no tempo de execução.

O gerenciador de segurança IoT Edge inclui três componentes:

* Daemon de segurança do IoT Edge.
* Camada de abstração da plataforma do módulo de segurança de hardware (HSM PAL).
* Raiz de silício de hardware opcional ou altamente recomendada de hardware ou HSM.

## <a name="the-iot-edge-security-daemon"></a>O daemon de segurança do IoT Edge

O daemon de segurança IoT Edge é o software responsável pelas operações lógicas do gerenciador de segurança IoT Edge. Ele é uma parte significativa da base de computação confiável do dispositivo IoT Edge. 

### <a name="design-principles"></a>Princípios de design

O daemon de segurança do IoT Edge segue dois princípios básicos: maximizar a integridade operacional e minimizar a sobrecarga e variações.

#### <a name="maximize-operational-integrity"></a>Maximizar a integridade operacional

O daemon de segurança IoT Edge funciona com a mais alta integridade possível dentro da capacidade de defesa de qualquer hardware de raiz de confiança. Com a integração adequada, a raiz do hardware de confiança mede e monitora o daemon de segurança estaticamente e em tempo de execução para resistir à violação.

O acesso físico é sempre uma ameaça aos dispositivos IoT. A raiz de hardware de confiança desempenha um papel importante na defesa da integridade do daemon de segurança do IoT Edge.  Raiz de hardware de confiança vêm em duas versões:

* elementos seguros para a proteção de informações confidenciais, como segredos e chaves criptográficas.
* enclaves seguros para a proteção de segredos, como chaves, e cargas de trabalho confidenciais, como medição e faturamento.

Existem dois tipos de ambientes de execução para utilizar a raiz de hardware de confiança:

* O ambiente de execução padrão ou rico (REE) que depende do uso de elementos seguros para proteger informações confidenciais.
* O ambiente de execução confiável (TEE) que depende do uso da tecnologia Secure Enclave para proteger informações confidenciais e oferecer proteção à execução de software.

Para dispositivos que usam enclaves seguros como raiz de hardware de confiança, espera-se que a lógica sensível do daemon de segurança IoT Edge resida dentro do enclave.  Partes não sensíveis do daemon de segurança podem residir fora do TEE.  Em qualquer caso, espera-se que os ODMs (fabricantes de projetos originais) e os OEMs (fabricantes de equipamentos originais) estendam a confiança de seu HSM para medir e defender a integridade do daemon de segurança IoT Edge no boot e no tempo de execução.

#### <a name="minimize-bloat-and-churn"></a>Minimizar a sobrecarga e a variação

Outro princípio fundamental para o daemon de segurança IoT Edge é minimizar a rotatividade.  Para obter o mais alto nível de confiança, o daemon de segurança IoT Edge pode se associar à raiz de hardware do dispositivo e operar como código nativo.  É comum que esses tipos de realizações atualizem o software daemon por meio dos caminhos de atualização segura da raiz de hardware da confiança (em oposição aos mecanismos de atualização fornecidos pelo SO), o que pode ser desafiador dependendo do hardware e do cenário de implantação específicos.  Embora a renovação de segurança seja uma forte recomendação para dispositivos IoT, é lógico que requisitos de atualização excessivos ou grandes cargas de atualização podem expandir a superfície de ameaças de várias maneiras.  Os exemplos incluem a omissão de atualizações para maximizar a disponibilidade operacional ou a raiz do hardware de confiança, muito restrita para processar grandes cargas úteis de atualização.  Como tal, o design do daemon de segurança IoT Edge é conciso para manter a área ocupada e, portanto, a base de computação confiável pequena e para minimizar os requisitos de atualização.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arquitetura do daemon de segurança do IoT Edge

![Daemon de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

O daemon de segurança IoT Edge é arquitetado para aproveitar qualquer raiz de hardware disponível da tecnologia de confiança para proteção de segurança.  Ele também permite a operação em ambientes divididos entre um REE (ambiente de execução rich)/padrão e um TEE (ambiente de execução confiável) quando as tecnologias de hardware oferecem ambientes de execução confiáveis. Interfaces específicas da função permitem a interação dos principais componentes do IoT Edge para garantir a integridade do dispositivo IoT Edge e suas operações.

#### <a name="cloud-interface"></a>Interface de nuvem

A interface da nuvem permite que o daemon de segurança IoT Edge acesse serviços em nuvem, como elogios na nuvem, para segurança de dispositivos, como a renovação de segurança.  Por exemplo, o daemon de segurança IoT Edge atualmente usa essa interface para acessar o [Serviço de Provisionamento de Dispositivo (DPS)](https://docs.microsoft.com/azure/iot-dps/) do Hub IoT do Azure para o gerenciamento do ciclo de vida da identidade do dispositivo.  

#### <a name="management-api"></a>API de gerenciamento

O daemon de segurança IoT Edge oferece uma API de gerenciamento, que é chamada pelo agente IoT Edge ao criar/iniciar/parar/remover um módulo de borda. O daemon de segurança do IoT Edge armazena "registros" para todos os módulos do Active Directory. Esses registros mapeiam a identidade de um módulo para algumas propriedades do módulo. Alguns exemplos dessas propriedades são o identificador do processo (pid) do processo em execução no contêiner ou o hash do conteúdo do contêiner do estivador.

Essas propriedades são usadas pela API de carga de trabalho (descrita abaixo) para atestar que o responsável pela chamada está autorizado a executar uma ação.

A API de gerenciamento é uma API privilegiada, que pode ser chamada somente pelo agente IoT Edge.  Como o daemon de segurança IoT Edge inicializa e inicia o agente IoT Edge, ele pode criar um registro implícito para o agente IoT Edge, após ter atestado que o agente IoT Edge não foi adulterado. O mesmo processo de atestado usado pela API da carga de trabalho é usado para restringir o acesso à API de gerenciamento apenas ao agente do IoT Edge.

#### <a name="container-api"></a>Contêiner de API

O daemon de segurança IoT Edge oferece a interface de contêiner para interagir com o sistema de contêiner em uso, como Moby e Docker, para instanciação de módulo.

#### <a name="workload-api"></a>API de carga de trabalho

A API de carga de trabalho é uma API do daemon de segurança IoT Edge acessível a todos os módulos, incluindo o agente IoT Edge. Ele fornece uma prova de identidade, seja um token assinado baseado em HSM, seja um certificado X509, bem como um pacote de confiança correspondente para um módulo. O pacote confiável contém certificados de Autoridade de Certificação para todos os outros servidores nos quais os módulos devem confiar.

O daemon de segurança IoT Edge usa um processo de atestado para proteger essa API. Quando um módulo chama essa API, o daemon de segurança IoT Edge tenta localizar um registro para a identidade. Se bem sucedido, usa as propriedades do registro para medir o módulo. Se o resultado do processo de medição corresponder ao registro, um novo token assinado baseado em HSM ou um certificado X509 será gerado. Os certificados de Autoridade de Certificação correspondentes (pacote confiável) serão retornados ao módulo.  O módulo usa esse certificado para se conectar ao Hub IoT, outros módulos ou iniciar um servidor. Quando o token ou certificado assinado estiver próximo da expiração, é de responsabilidade do módulo solicitar um novo certificado. 

### <a name="integration-and-maintenance"></a>Integração e manutenção

A Microsoft mantém a principal base de código para o daemon de segurança [IoT Edge no GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalação e as atualizações

A instalação e as atualizações do daemon de segurança IoT Edge são gerenciadas por meio do sistema de gerenciamento de pacotes do sistema operacional. Os dispositivos IoT Edge com raiz de hardware de confiança devem fornecer proteção adicional à integridade do daemon, gerenciando seu ciclo de vida por meio dos sistemas de gerenciamento seguro de inicialização e de atualizações.  Cabe aos fabricantes de dispositivos explorar esses caminhos de acordo com seus respectivos recursos de dispositivos.

#### <a name="versioning"></a>Controle de versão

O tempo de execução IoT Edge rastreia e relata a versão do daemon de segurança IoT Edge. A versão é relatada como o atributo *runtime.platform.version* da propriedade relatada do módulo do agente IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Camada de abstração de plataforma hardware security module (HSM PAL)

O HSM PAL abstrai toda a raiz do hardware de confiança para isolar o desenvolvedor ou usuário do IoT Edge de suas complexidades.  Ele compreende uma combinação de procedimentos de comunicação de interface de programação de aplicativo (API) e domínios trans, por exemplo, comunicação entre um ambiente de execução padrão e um enclave seguro.  A implementação real do HSM PAL depende do hardware seguro específico em uso. Sua existência permite o uso de praticamente qualquer hardware de silício seguro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Raiz de silício seguro do hardware de confiança

O silício seguro é necessário para ancorar a confiança dentro do hardware do dispositivo IoT Edge.  O silício seguro é variado para incluir o Trusted Platform Module (TPM), o Secure Element (eSE) incorporado, o ARM TrustZone, o Intel SGX e as tecnologias de silício seguras personalizadas.  O uso de raiz de silício seguro de confiança em dispositivos é altamente recomendado, dadas as ameaças associadas à acessibilidade física de dispositivos IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integração e manutenção do gerenciador de segurança IoT Edge

O objetivo do gerenciador de segurança do IoT Edge é identificar e isolar os componentes que defendem a segurança e a integridade da plataforma Azure IoT Edge para proteção personalizada. Terceiros, como fabricantes de dispositivos, devem fazer uso de recursos de segurança personalizados disponíveis com o hardware do dispositivo.  Confira a próxima seção de etapas para links que demonstram como proteger o gerenciador de segurança da IoT do Azure com o TPM (Trusted Platform Module) nas plataformas Linux e Windows. Esses exemplos usam software ou TPMs virtuais, mas se aplicam diretamente ao uso de dispositivos TPM discretos.  

## <a name="next-steps"></a>Próximos passos

Leia o blog em [ Protegendo a borda inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Crie e provisione um [dispositivo do IoT Edge com uma TPM virtual em uma máquina virtual do Linux](how-to-auto-provision-simulated-device-linux.md).

Crie e provisione um [dispositivo IoT Edge com um TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md).
