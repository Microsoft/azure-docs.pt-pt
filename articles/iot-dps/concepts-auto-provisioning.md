---
title: Serviço de provisionamento de dispositivos no Hub IoT-conceitos de provisionamento automático
description: Este artigo fornece uma visão geral conceitual das fases do provisionamento automático do dispositivo, usando o DPS (serviço de provisionamento de dispositivos IoT), o Hub IoT e os SDKs do cliente.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975334"
---
# <a name="auto-provisioning-concepts"></a>Conceitos de provisionamento automático

Conforme descrito na [visão geral](about-iot-dps.md), o serviço de provisionamento de dispositivos é um serviço auxiliar que permite o provisionamento just-in-time de dispositivos em um hub IOT, sem a necessidade de intervenção humana. Após o provisionamento bem-sucedido, os dispositivos se conectam diretamente com o Hub IoT designado. Esse processo é conhecido como provisionamento automático e fornece um registro pronto para uso e uma experiência de configuração inicial para os dispositivos.

## <a name="overview"></a>Visão geral

O provisionamento automático de IoT do Azure pode ser dividido em três fases:

1. **Configuração de serviço** – uma configuração única do Hub IOT do Azure e instâncias do serviço de provisionamento de dispositivos do Hub IOT, estabelecendo-as e criando vínculos entre elas.

   > [!NOTE]
   > Independentemente do tamanho da sua solução de IoT, mesmo que você planeje dar suporte a milhões de dispositivos, essa é uma **configuração única**.

2. **Registro de dispositivo** – o processo de fazer com que a instância do serviço de provisionamento de dispositivos reconheça os dispositivos que tentarão se registrar no futuro. O [registro](concepts-service.md#enrollment) é realizado com a configuração de informações de identidade do dispositivo no serviço de provisionamento, como um "registro individual" para um único dispositivo ou um "registro de grupo" para vários dispositivos. A identidade se baseia no [mecanismo de atestado](concepts-security.md#attestation-mechanism) que o dispositivo foi projetado para usar, o que permite ao serviço de provisionamento atestar a autenticidade do dispositivo durante o registro:

   - **TPM**: configurado como um "registro individual", a identidade do dispositivo é baseada na ID de registro do TPM e na chave de endosso pública. Considerando que o TPM é uma [especificação](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), o serviço só espera atestar de acordo com a especificação, independentemente da implementação do TPM (hardware ou software). Confira [provisionamento de dispositivos: atestado de identidade com TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) para obter detalhes sobre o atestado baseado em TPM. 

   - **X509**: configurado como um "registro individual" ou "registro de grupo", a identidade do dispositivo é baseada em um certificado digital X. 509, que é carregado no registro como um arquivo. PEM ou. cer.

   > [!IMPORTANT]  
   > Embora não seja um pré-requisito para usar os serviços de provisionamento de dispositivos, é altamente recomendável que seu dispositivo use um HSM (módulo de segurança de hardware) para armazenar informações confidenciais de identidade do dispositivo, como chaves e certificados X. 509.

3. **Registro de dispositivo e configuração** -iniciados após a inicialização por software de registro, que é criado usando um SDK de cliente do serviço de provisionamento de dispositivos apropriado para o mecanismo de dispositivo e atestado. O software estabelece uma conexão com o serviço de provisionamento para autenticação do dispositivo e o registro subsequente no Hub IoT. Após o registro bem-sucedido, o dispositivo é fornecido com sua ID de dispositivo e informações de conexão exclusivas do Hub IoT, permitindo que ele extraia sua configuração inicial e inicie o processo de telemetria. Em ambientes de produção, essa fase pode ocorrer semanas ou meses após as duas fases anteriores.

## <a name="roles-and-operations"></a>Funções e operações

As fases discutidas na seção anterior podem abranger semanas ou meses, devido a realidades de produção, como tempo de fabricação, envio, processo alfandegário etc. Além disso, eles podem abranger atividades em várias funções dadas as várias entidades envolvidas. Esta seção analisa mais detalhadamente as várias funções e operações relacionadas a cada fase e, em seguida, ilustra o fluxo em um diagrama de sequência. 

O provisionamento automático também coloca os requisitos no fabricante do dispositivo, especificamente para habilitar o mecanismo de atestado. As operações de manufatura também podem ocorrer independentemente do tempo das fases de provisionamento automático, especialmente em casos em que novos dispositivos são adquiridos após o provisionamento automático já ter sido estabelecido.

Uma série de guias de início rápido é fornecida no sumário à esquerda, para ajudar a explicar o provisionamento automático por meio de experiência prática. Para facilitar/simplificar o processo de aprendizado, o software é usado para simular um dispositivo físico para registro e registro. Alguns guias de início rápido exigem que você realize operações para várias funções, incluindo operações para funções inexistentes, devido à natureza simulada dos guias de início rápido.

| Função | Operação | Descrição |
|------| --------- | ------------|
| Fabricante | Codificar a identidade e a URL de registro | Com base no mecanismo de atestado usado, o fabricante é responsável por codificar as informações de identidade do dispositivo e a URL de registro do serviço de provisionamento de dispositivos.<br><br>**Início rápido**: como o dispositivo é simulado, não há nenhuma função de fabricante. Consulte a função de desenvolvedor para obter detalhes sobre como obter essas informações, que são usadas na codificação de um aplicativo de registro de exemplo. |
| | Fornecer identidade do dispositivo | Como o originador das informações de identidade do dispositivo, o fabricante é responsável por comunicar-se com o operador (ou um agente designado) ou registrá-lo diretamente no serviço de provisionamento de dispositivos por meio de APIs.<br><br>**Início rápido**: como o dispositivo é simulado, não há nenhuma função de fabricante. Consulte a função operador para obter detalhes sobre como você obtém a identidade do dispositivo, que é usada para registrar um dispositivo simulado em sua instância do serviço de provisionamento de dispositivos. |
| Operador | Configurar provisionamento automático | Esta operação corresponde à primeira fase do provisionamento automático.<br><br>**Início rápido**: você executa a função de operador, configurando o serviço de provisionamento de dispositivos e instâncias de Hub IOT em sua assinatura do Azure. |
|  | Registrar identidade do dispositivo | Esta operação corresponde à segunda fase do provisionamento automático.<br><br>**Início rápido**: você executa a função de operador, registrando o dispositivo simulado em sua instância do serviço de provisionamento de dispositivos. A identidade do dispositivo é determinada pelo método de atestado que está sendo simulado no início rápido (TPM ou X. 509). Consulte a função de desenvolvedor para obter detalhes de atestado. |
| Serviço de provisionamento de dispositivos,<br>Hub IoT | \<todas as operações\> | Para uma implementação de produção com dispositivos físicos e guias de início rápido com dispositivos simulados, essas funções são atendidas por meio dos serviços de IoT que você configura em sua assinatura do Azure. As funções/operações funcionam exatamente da mesma forma, pois os serviços de IoT são diferentes para o provisionamento de dispositivos físicos vs. simulados. |
| Programador | Compilar/implantar software de registro | Esta operação corresponde à terceira fase de provisionamento automático. O desenvolvedor é responsável por criar e implantar o software de registro no dispositivo, usando o SDK apropriado.<br><br>**Início rápido**: o aplicativo de registro de exemplo que você cria simula um dispositivo real, para sua plataforma/idioma de sua escolha, que é executado em sua estação de trabalho (em vez de implantá-lo em um dispositivo físico). O aplicativo de registro executa as mesmas operações que uma implantada em um dispositivo físico. Você especifica o método de atestado (certificado TPM ou X. 509), além da URL de registro e "escopo da ID" da instância do serviço de provisionamento de dispositivos. A identidade do dispositivo é determinada pela lógica de atestado do SDK em tempo de execução, com base no método especificado: <ul><li>**Atestado de TPM** -sua estação de trabalho de desenvolvimento executa um [aplicativo de simulador de TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Uma vez em execução, um aplicativo separado é usado para extrair a "chave de endosso" e a "ID de registro" do TPM para uso no registro da identidade do dispositivo. A lógica de atestado do SDK também usa o simulador durante o registro, para apresentar um token SAS assinado para autenticação e verificação de registro.</li><li>**Atestado X509** -você usa uma ferramenta para [gerar um certificado](how-to-use-sdk-tools.md#x509-certificate-generator). Depois de gerado, você cria o arquivo de certificado necessário para uso no registro. A lógica de atestado do SDK também usa o certificado durante o registro, para apresentar a autenticação e a verificação de registro.</li></ul> |
| Dispositivo | Inicialização e registro | Essa operação corresponde à terceira fase de provisionamento automático, atendida pelo software de registro de dispositivo criado pelo desenvolvedor. Consulte a função de desenvolvedor para obter detalhes. Após a primeira inicialização: <ol><li>O aplicativo se conecta com a instância do serviço de provisionamento de dispositivos, de acordo com a URL global e o "escopo de ID" do serviço especificado durante o desenvolvimento.</li><li>Uma vez conectado, o dispositivo é autenticado em relação ao método de atestado e à identidade especificada durante o registro.</li><li>Uma vez autenticado, o dispositivo é registrado com a instância do Hub IoT especificada pela instância do serviço de provisionamento.</li><li>Após o registro bem-sucedido, uma ID de dispositivo exclusiva e um ponto de extremidade do Hub IoT são retornados para o aplicativo de registro para comunicação com o Hub IoT.</li><li> A partir daí, o dispositivo pode extrair o estado inicial do seu [dispositivo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) para configuração e iniciar o processo de relatório de dados de telemetria.</li></ol>**Início rápido**: como o dispositivo é simulado, o software de registro é executado em sua estação de trabalho de desenvolvimento.|

O diagrama a seguir resume as funções e o sequenciamento de operações durante o provisionamento automático do dispositivo:
<br><br>
[![sequência de provisionamento automático para um dispositivo](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcionalmente, o fabricante também pode executar a operação "registrar a identidade do dispositivo" usando APIs do serviço de provisionamento de dispositivos (em vez de por meio do operador). Para obter uma discussão detalhada sobre esse sequenciamento e muito mais, consulte o [registro do dispositivo Zero Touch com o vídeo do IOT do Azure](https://youtu.be/cSbDRNg72cU?t=2460) (começando no marcador 41:00)

## <a name="roles-and-azure-accounts"></a>Funções e contas do Azure

A forma como cada função é mapeada para uma conta do Azure é dependente do cenário e há alguns cenários que podem ser envolvidos. Os padrões comuns abaixo devem ajudar a fornecer um entendimento geral sobre como as funções são geralmente mapeadas para uma conta do Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>O fabricante do chip fornece serviços de segurança

Nesse cenário, o fabricante gerencia a segurança para clientes de nível um. Esse cenário pode ser preferido por esses clientes de nível 1, pois eles não precisam gerenciar a segurança detalhada. 

O fabricante apresenta a segurança em HSMs (módulos de segurança de hardware). Essa segurança pode incluir o fabricante que obtém chaves, certificados, etc. de clientes potenciais que já têm instâncias de DPS e configuração de grupos de registro. O fabricante também poderia gerar essas informações de segurança para seus clientes.

Nesse cenário, pode haver duas contas do Azure envolvidas:

- **#1 de conta**: provavelmente compartilhado entre as funções de operador e de desenvolvedor em algum grau. Essa parte pode comprar os chips HSM do fabricante. Esses chips são apontados para instâncias do DPS associadas à conta #1. Com os registros de DPS, essa parte pode conceder dispositivos a vários clientes de dois níveis, redefinindo as configurações de registro do dispositivo no DPS. Essa parte também pode ter hubs IoT alocados para que os sistemas de back-end do usuário final se desorganizem para acessar a telemetria do dispositivo etc. Neste último caso, uma segunda conta pode não ser necessária.

- **Conta #2**: usuários finais, os clientes de nível dois podem ter seus próprios hubs IOT. A parte associada à conta #1 apenas aponta os dispositivos concedidos para o Hub correto nessa conta. Essa configuração requer a vinculação de um DPS e de hubs IoT em contas do Azure, o que pode ser feito com modelos de Azure Resource Manager.

#### <a name="all-in-one-oem"></a>OEM All-in-One

O fabricante pode ser um "OEM" All-in-One ", no qual apenas uma única conta de fabricante seria necessária. O fabricante lida com a segurança e o provisionamento de ponta a ponta.

O fabricante pode fornecer um aplicativo baseado em nuvem aos clientes que comprarem dispositivos. Esse aplicativo faria a interface com o Hub IoT alocado pelo fabricante.

Máquinas de vendas ou máquinas de café automatizadas representam exemplos para esse cenário.




## <a name="next-steps"></a>Passos seguintes

Talvez seja útil marcar este artigo como um ponto de referência, à medida que você trabalhar com os guias de início rápido de provisionamento automático correspondentes. 

Comece concluindo um início rápido "configurar provisionamento automático" que melhor atenda à sua preferência de ferramenta de gerenciamento, que orienta você pela fase de "configuração do serviço":

- [Configurar o provisionamento automático usando o CLI do Azure](quick-setup-auto-provision-cli.md)
- [Configurar o provisionamento automático usando o portal do Azure](quick-setup-auto-provision.md)
- [Configurar o provisionamento automático usando um modelo do Resource Manager](quick-setup-auto-provision-rm.md)

Em seguida, continue com um início rápido "provisionamento automático de um dispositivo simulado" que atenda ao mecanismo de atestado de dispositivo e à preferência de SDK/idioma do serviço de provisionamento de dispositivos. Neste guia de início rápido, você percorre as fases "registro do dispositivo" e "registro e configuração do dispositivo": 

|  | Mecanismo de atestado de dispositivo simulado | SDK/idioma de início rápido |  |
|--|--|--|--|
|  | TPM (Trusted Platform Module) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certificado X. 509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




