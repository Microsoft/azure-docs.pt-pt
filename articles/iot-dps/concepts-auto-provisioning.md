---
title: Serviço de aprovisionamento de dispositivos no IoT Hub - conceitos de aprovisionamento automático
description: Este artigo fornece uma descrição geral conceptual das fases de auto-aprovisionamento de dispositivos, através do serviço de aprovisionamento de dispositivos do IoT, IoT Hub e SDKs do cliente.
author: wesmc7777
ms.author: wesmc
ms.date: 06/01/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 6ba21e29673da176c380d6b367e84b2ea7536955
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080036"
---
# <a name="auto-provisioning-concepts"></a>Conceitos de aprovisionamento automático

Conforme descrito no [descrição geral](about-iot-dps.md), o serviço de aprovisionamento de dispositivos é um serviço de programa auxiliar que permite o just-in-time aprovisionamento de dispositivos para um hub IoT, sem a necessidade de intervenção humana. Após o aprovisionamento com êxito, os dispositivos se ligam diretamente com o seu IoT Hub designado. Este processo é conhecido como aprovisionamento automático e fornece que um registo de out-of-the-box e a configuração inicial experiência para dispositivos.

## <a name="overview"></a>Descrição geral

O Azure IoT aprovisionamento automático pode ser dividido em três fases:

1. **Configuração do serviço** -uma configuração única das instâncias IoT Hub do Azure e o serviço de aprovisionamento de dispositivo IoT Hub, estabelecendo-los e criar a ligação entre eles.

   > [!NOTE]
   > Independentemente do tamanho da sua solução de IoT, mesmo que pretende suportar milhões de dispositivos, este é um **configuração única**.

2. **Inscrição de dispositivos** -o processo de efetuar a instância do serviço aprovisionamento de dispositivos com suporte para os dispositivos que irá tentar registar no futuro. [Inscrição](concepts-service.md#enrollment) é feito ao configurar informações de identidade de dispositivo no serviço de aprovisionamento, como uma "inscrição individual" para um único dispositivo ou uma "inscrição de grupo" para vários dispositivos. Identidade se baseia a [mecanismo de atestado](concepts-security.md#attestation-mechanism) o dispositivo foi projetado para usar, que permite que o serviço de aprovisionamento atestar a autenticidade do dispositivo durante o registo:

   - **TPM**: configurado como uma "inscrição individual", a identidade de dispositivo se baseia o ID de registo TPM e a chave de endossamento pública. Uma vez que o TPM é um [especificação](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), o serviço espera apenas atestar de acordo com a especificação, independentemente da implementação de TPM (hardware ou software). Consulte [aprovisionamento de dispositivos: Atestado de identidades com TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) para obter detalhes sobre um atestado baseado no TPM. 

   - **X509**: configurado como um "inscrição individual" ou "inscrição de grupo", a identidade de dispositivo se baseia num certificado digital X.509, que é carregado para a inscrição como um ficheiro. pem ou. cer.

   > [!IMPORTANT]  
   > Embora não é um pré-requisito para utilizar os serviços de aprovisionamento de dispositivo, é altamente recomendável que a utilização de dispositivo um módulo de segurança de Hardware (HSM) para armazenar informações de identidade de dispositivo confidenciais, tais como chaves e certificados X.509.

3. **Registo de dispositivos e a configuração** - iniciada após a inicialização de cópia de segurança pelo software de registo, o que é criada usando um SDK adequado para o mecanismo de atestado e dispositivos de cliente do serviço aprovisionamento de dispositivos. O software estabelece uma ligação ao serviço de aprovisionamento para a autenticação do dispositivo e registo subsequente no IoT Hub. Após o registo com êxito, o dispositivo é fornecido com o seu IoT Hub exclusivo ID e a ligação de informações do dispositivo, permitindo-lhe extrair a respetiva configuração inicial e iniciar o processo de telemetria. Em ambientes de produção, esta fase pode ocorrer semanas ou meses depois de duas fases anteriores.

## <a name="roles-and-operations"></a>Funções e operações

As fases discutidas na seção anterior podem ir de semanas ou meses, devido a realidades da produção, como o tempo de fabrico, de envio, o processo de alfândega, etc. Além disso, eles podem ser distribuídas atividades em várias funções, tendo em conta as várias entidades envolvidas. Esta secção assume uma análise mais profunda a várias funções e as operações relacionadas para cada fase, em seguida, ilustra o fluxo num diagrama de sequência. 

Também aprovisionamento automático transfere os requisitos do fabricante do dispositivo, específico para ativar o mecanismo de atestação. Operações de fabricação também podem ocorrer independentemente do período de tempo de aprovisionamento automático fases, especialmente em casos em que os novos dispositivos são fornecidos após o aprovisionamento automático já foi estabelecido.

Uma série de guias de introdução são fornecidos no índice à esquerda, para ajudar a explicar o aprovisionamento automático através de uma experiência prática. Para facilitar a/simplificar o processo de aprendizado, o software é usado para simular um dispositivo físico para inscrição e o Registro. Alguns guias de introdução requerem que atenda a operações de várias funções, incluindo operações para funções de não existente, devido à natureza simulada dos inícios rápidos.

| Função | Operação | Descrição |
|------| --------- | ------------|
| Fabricante | Codificar o URL de identidade e de registo | Com base no mecanismo de atestado utilizado, o fabricante é responsável pela codificação as informações de identidade de dispositivo e o URL de registo do serviço aprovisionamento de dispositivos.<br><br>**Inícios Rápidos**: uma vez que o dispositivo é simulado, não existe nenhuma função de fabricante. Consulte a função de desenvolvedor para obter detalhes sobre como obter estas informações, o que são utilizadas na codificação de uma aplicação de registo de exemplo. |
| | Fornecer a identidade de dispositivo | Como o originador as informações de identidade de dispositivo, o fabricante é responsável por comunicá-lo para o operador (ou um agente designado) ou inscrevê-lo diretamente para o serviço de aprovisionamento de dispositivos através de APIs.<br><br>**Inícios Rápidos**: uma vez que o dispositivo é simulado, não existe nenhuma função de fabricante. Consulte a função de operador para obter detalhes sobre como obter a identidade de dispositivo, o que é utilizada para inscrever um dispositivo simulado na sua instância do serviço aprovisionamento de dispositivos. |
| Operador | Configurar o aprovisionamento automático | Esta operação corresponde com a primeira fase de aprovisionamento automático.<br><br>**Inícios Rápidos**: Executar a função de operador, configurar as instâncias do serviço aprovisionamento de dispositivos e IoT Hub na sua subscrição do Azure. |
|  | Inscrever a identidade de dispositivo | Esta operação corresponde com a segunda fase de aprovisionamento automático.<br><br>**Inícios Rápidos**: Executar a função de operador, inscrever o seu dispositivo simulado na sua instância do serviço aprovisionamento de dispositivos. A identidade de dispositivo é determinada pelo método de atestado que está a ser simulado no início rápido (TPM ou X.509). Consulte a função de desenvolvedor para obter detalhes de atestado. |
| Serviço de aprovisionamento de dispositivo,<br>IoT Hub | \<todas as operações\> | Para tanto uma implementação de produção com dispositivos físicos e guias de introdução com dispositivos simulados, estas funções são atendidas por meio dos serviços de IoT que configurar na sua subscrição do Azure. As funções/operações funcionam exatamente da mesma forma, os serviços de IoT são indiferentes para o aprovisionamento do físicos versus dispositivos simulados. |
| Programador | Build/Deploy software de registo | Esta operação corresponde com a terceira fase de aprovisionamento automático. O desenvolvedor é responsável por criar e implementar o software de registo para o dispositivo, utilizando o SDK adequado.<br><br>**Inícios Rápidos**: A aplicação de registo de exemplo que cria simula um dispositivo real, para a plataforma/linguagem de sua escolha, que é executado na sua estação de trabalho (em vez de implementar a política para um dispositivo físico). A aplicação de registo executa as mesmas operações como um implementada para um dispositivo físico. Especifique o método de atestado (certificado de TPM ou X.509), além do URL de registo e o "ID de escopo" da sua instância do serviço aprovisionamento de dispositivos. A identidade de dispositivo é determinada pela lógica de atestado de SDK no tempo de execução, com base no método que especificar: <ul><li>**Atestado de TPM** -a estação de trabalho de desenvolvimento é executada uma [aplicativo de simulador TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Quando em execução, um aplicativo separado é utilizado para extrair o TPM "Chave de endossamento" e "ID de registo" para utilização na inscrição a identidade de dispositivo. A lógica de atestado de SDK também utiliza o simulador durante o registo, para apresentar um token SAS assinado para autenticação e a verificação de inscrição.</li><li>**X509 atestado** -é usar uma ferramenta para [gerar um certificado](how-to-use-sdk-tools.md#x509-certificate-generator). Depois de gerada, criar o ficheiro de certificado necessário para utilização na inscrição. A lógica de atestado de SDK também utiliza o certificado durante o registo, para apresentar para autenticação e a verificação de inscrição.</li></ul> |
| Dispositivo | Inicialização e registre-se | Esta operação corresponde com a terceira fase de aprovisionamento automático, atendida pelo software de registo do dispositivo criado pelo desenvolvedor. Consulte a função de desenvolvedor para obter detalhes. Na primeira inicialização: <ol><li>O aplicativo se conecta à instância do serviço aprovisionamento de dispositivos, pelo URL e o serviço "ID de âmbito" especificado durante o desenvolvimento global.</li><li>Assim que estiver ligado, o dispositivo é autenticado com o método de atestado e a identidade especificada durante a inscrição.</li><li>Uma vez autenticado, o dispositivo está registado com a instância do IoT Hub especificada pela instância do serviço de aprovisionamento.</li><li>Após o registo com êxito, um ponto de extremidade do IoT Hub e o ID exclusivo do dispositivo são devolvidos para a aplicação de registo para a comunicação com o IoT Hub.</li><li> A partir daí, o dispositivo pode obter sua inicial [dispositivo duplo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) para configuração de estado e iniciar o processo de geração de relatórios de dados de telemetria.</li></ol>**Inícios Rápidos**: uma vez que o dispositivo é simulado, o software de registo é executado na sua estação de trabalho de desenvolvimento.|

O diagrama seguinte resume as funções e a seqüência de operações durante o aprovisionamento automático do dispositivo:
<br><br>
[![Sequência de aprovisionamento automático de mensagens em fila para um dispositivo](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcionalmente, o fabricante também pode efetuar a operação "Inscrever a identidade de dispositivo", com APIs de serviço de aprovisionamento de dispositivos (em vez de através do operador). Para uma discussão detalhada sobre esse sequenciamento e muito mais, consulte a [registo de dispositivos Zero touch com vídeo do Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (começando em 41:00 de marcador)

## <a name="roles-and-azure-accounts"></a>Funções e contas do Azure

Como cada função é mapeada para uma conta do Azure é dependente do cenário e existem alguns cenários que podem estar envolvidos. Os padrões comuns abaixo devem ajudar a fornecer uma compreensão geral sobre como as funções geralmente são mapeadas para uma conta do Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Fabricante do chip fornece serviços de segurança

Neste cenário, o fabricante gerencia a segurança para clientes de um nível. Este cenário poderá ser preferencial por estes clientes de um nível que não precisam de gerir a segurança detalhada. 

O fabricante apresenta a segurança em módulos de segurança de Hardware (HSMs). Esta segurança pode incluir o fabricante de obtenção de chaves, certificados, etc. de potenciais clientes que já têm instâncias de pontos de distribuição e a configuração de grupos de inscrição. O fabricante também poderia gerar essas informações de segurança para seus clientes.

Neste cenário, pode haver duas contas do Azure envolvidas:

- **Conta #1**: Provavelmente, é compartilhado entre as funções do operador e desenvolvedor até certo ponto. Essa parte pode adquirir os chips de HSM do fabricante. Estes chips estão apontava para instâncias de pontos de distribuição associadas com a conta de n. º 1. Com as inscrições de pontos de distribuição, essa parte pode alugar dispositivos para vários clientes de dois níveis ao reconfigurar as definições de inscrição de dispositivos no DPS. Essa parte confiável também pode ter os hubs IoT alocados para sistemas de back-end do utilizador final para fazer a interface com, para poder aceder a telemetria do dispositivo etc. Neste último caso, pode não ser necessária uma segunda conta.

- **Conta #2**: Os utilizadores finais, dois clientes podem ter seus próprios hubs IoT. A entidade associada a pontos de apenas 1 conta concedidos dispositivos no hub correto nesta conta. Esta configuração requer que estabelece ligação hubs IoT e de pontos de distribuição entre as contas do Azure, que podem ser feitas com modelos Azure Resource Manager.

#### <a name="all-in-one-oem"></a>Tudo-em-um OEM

O fabricante pode ser um OEM"tudo-em-um" onde seria necessário apenas uma conta de fabricante único. O fabricante lida com segurança e provisionamento de ponto a ponto.

O fabricante poderá fornecer uma aplicação baseada na cloud para os clientes que compram dispositivos. Esta aplicação teria de interface com o IoT Hub atribuída pelo fabricante.

Máquinas de venda automática ou máquinas de café automatizada representam exemplos para este cenário.




## <a name="next-steps"></a>Passos Seguintes

Talvez seja útil marcar este artigo como um ponto de referência, à medida que trabalha seu caminho pela inícios rápidos correspondentes de aprovisionamento automático. 

Comece por concluir um início rápido "Configurar o aprovisionamento automático" mais adequado às suas preferências de ferramenta de gestão, que explica como a fase de "Configuração de serviço":

- [Configurar o aprovisionamento automático com a CLI do Azure](quick-setup-auto-provision-cli.md)
- [Configurar o aprovisionamento automático com o portal do Azure](quick-setup-auto-provision.md)
- [Configurar o aprovisionamento automático através de um modelo do Resource Manager](quick-setup-auto-provision-rm.md)

Em seguida, continue com um guia de introdução do "Aprovisionar automaticamente um dispositivo simulado" que lhe seja conveniente seu mecanismo de atestado de dispositivo e a preferência do SDK do serviço aprovisionamento de dispositivos/linguagem. Neste início rápido, que pode percorrer as fases de "Inscrição de dispositivos" e "registo de dispositivos e a configuração": 

|  | Mecanismo de atestado de dispositivo simulado | Início rápido SDK/linguagem |  |
|--|--|--|--|
|  | Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certificado X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




