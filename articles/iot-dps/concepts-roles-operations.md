---
title: Serviço de Fornecimento de Dispositivos IoT Hub - Funções e operações
description: Este artigo fornece uma visão geral conceptual das funções e operações envolvidas no desenvolvimento e solução IoT utilizando o Serviço de Provisionamento de Dispositivos IoT (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 52c0dcf4bd9f7b2ca8928eab5272a6500cbb379d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533076"
---
# <a name="roles-and-operations"></a>Funções e operações

As fases de desenvolvimento de uma solução IoT podem estender-se semanas ou meses, devido a realidades de produção como tempo de fabrico, transporte, processo aduaneiro, etc. Além disso, podem abranger atividades em múltiplas funções, dadas as várias entidades envolvidas. Este tópico analisa mais profundamente as várias funções e operações relacionadas com cada fase, ilustrando então o fluxo num diagrama de sequência. 

O provisionamento também coloca requisitos no fabricante do dispositivo, específicos para permitir o [mecanismo de atestado](concepts-service.md#attestation-mechanism). As operações de fabrico podem igualmente ocorrer independentemente do calendário das fases de auto-provisão, especialmente nos casos em que novos dispositivos são adquiridos após o fornecimento automático já estabelecido.

Uma série de Quickstarts são fornecidos na tabela de conteúdos à esquerda, para ajudar a explicar o fornecimento automático através da experiência prática. Para facilitar/simplificar o processo de aprendizagem, o software é utilizado para simular um dispositivo físico de inscrição e registo. Alguns Quickstarts exigem que cumpra operações para múltiplas funções, incluindo operações para funções inexistentes, devido à natureza simulada dos Quickstarts.

| Função | Operação | Descrição |
|------| --------- | ------------|
| Fabricante | Codificar a identidade e o registo URL | Com base no mecanismo de atestado utilizado, o fabricante é responsável pela codificação da informação de identidade do dispositivo e pelo URL de registo do Serviço de Provisionamento de Dispositivos.<br><br>**Arranques rápidos**: uma vez que o dispositivo é simulado, não existe qualquer papel do Fabricante. Consulte a função developer para obter detalhes sobre como obtém esta informação, que é usada na codificação de um pedido de registo de amostra. |
| | Fornecer identidade do dispositivo | Como autor da informação de identidade do dispositivo, o fabricante é responsável pela sua comunicação ao operador (ou agente designado), ou pela sua inscrição direta no Serviço de Provisionamento de Dispositivos através de APIs.<br><br>**Arranques rápidos**: uma vez que o dispositivo é simulado, não existe qualquer papel do Fabricante. Consulte a função do Operador para obter mais detalhes sobre como obter a identidade do dispositivo, que é usada para inscrever um dispositivo simulado na sua instância do Serviço de Provisionamento de Dispositivos. |
| Operador | Configurar o fornecimento automático | Esta operação corresponde à primeira fase de provisão automática.<br><br>**Quickstarts**: Executa a função de Operador, configurando o Serviço de Provisionamento de Dispositivos e as instâncias IoT Hub na sua subscrição Azure. |
|  | Identidade do dispositivo de inscrição | Esta operação corresponde à segunda fase de provisão automática.<br><br>**Quickstarts**: Executa a função de Operador, inscrevendo o seu dispositivo simulado na sua instância de Serviço de Provisionamento de Dispositivos. A identidade do dispositivo é determinada pelo método de atestado que está a ser simulado no Quickstart (TPM ou X.509). Consulte o papel de Desenvolvedor para obter detalhes da atestação. |
| Serviço de Fornecimento de Dispositivos,<br>IoT Hub | \<all operations\> | Tanto para uma implementação de produção com dispositivos físicos, como quickstarts com dispositivos simulados, estas funções são cumpridas através dos serviços IoT que configura na sua subscrição Azure. As funções/operações funcionam exatamente da mesma forma, uma vez que os serviços IoT são indiferentes ao fornecimento de dispositivos físicos vs. simulados. |
| Programador | Software de registo de construção/implantação | Esta operação corresponde à terceira fase de provisão automática. O Desenvolvedor é responsável pela construção e implantação do software de registo no dispositivo, utilizando o SDK apropriado.<br><br>**Quickstarts**: A aplicação de registo de amostras que constrói simula um dispositivo real, para a sua plataforma/linguagem de eleição, que funciona na sua estação de trabalho (em vez de a implantar num dispositivo físico). O pedido de registo realiza as mesmas operações que uma implantada num dispositivo físico. Especifica o método de atestado (certificado TPM ou X.509), além do URL de registo e "ID Scope" da sua instância do Serviço de Provisionamento de Dispositivos. A identidade do dispositivo é determinada pela lógica de atestado SDK no tempo de execução, com base no método especificado: <ul><li>**Atestado TPM** - o seu posto de trabalho de desenvolvimento executa uma [aplicação de simulador TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Uma vez em funcionamento, é utilizada uma aplicação separada para extrair a "Chave de Endosso" e "ID de registo" do TPM para utilização na inscrição da identidade do dispositivo. A lógica de atestado SDK também utiliza o simulador durante o registo, para apresentar um token SAS assinado para verificação de autenticação e inscrição.</li><li>**Atestado X509** - utiliza uma ferramenta para [gerar um certificado](how-to-use-sdk-tools.md#x509-certificate-generator). Uma vez gerado, cria-se o ficheiro de certificado necessário para ser utilizado na inscrição. A lógica de atestado SDK também utiliza o certificado durante o registo, para apresentar para autenticação e verificação de inscrição.</li></ul> |
| Dispositivo | Arranque e registo | Esta operação corresponde à terceira fase de fornecimento automático, preenchida pelo software de registo do dispositivo construído pelo Desenvolvedor. Consulte o papel de Desenvolvedor para mais detalhes. Após a primeira bota: <ol><li>A aplicação conecta-se com a instância do Serviço de Provisionamento de Dispositivos, de acordo com o URL global e o serviço "ID Scope" especificado durante o desenvolvimento.</li><li>Uma vez ligado, o dispositivo é autenticado contra o método de atestado e a identidade especificados durante a inscrição.</li><li>Uma vez autenticado, o dispositivo é registado na instância IoT Hub especificada pela instância de serviço de fornecimento.</li><li>Após o registo bem sucedido, um dispositivo único ID e ioT Hub são devolvidos ao pedido de registo para comunicação com o IoT Hub.</li><li> A partir daí, o dispositivo pode puxar para baixo o seu estado [gémeo do dispositivo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) inicial para configuração, e iniciar o processo de reporte de dados de telemetria.</li></ol>**Quickstarts**: uma vez que o dispositivo é simulado, o software de registo funciona na sua estação de trabalho de desenvolvimento.|

O diagrama que se segue resume as funções e a sequência de operações durante o fornecimento automático do dispositivo:
<br><br>
[![Sequência de provisão automática para um dispositivo](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcionalmente, o fabricante também pode efetuar a operação "Inscrever identidade do dispositivo" utilizando APIs do Serviço de Provisionamento de Dispositivos (em vez de através do Operador). Para uma discussão detalhada sobre esta sequência e muito mais, consulte o registo do [dispositivo de toque Zero com vídeo Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (a partir do marcador 41:00)

## <a name="roles-and-azure-accounts"></a>Papéis e contas Azure

Como cada papel é mapeado para uma conta Azure é dependente de cenário, e há alguns cenários que podem estar envolvidos. Os padrões comuns abaixo devem ajudar a fornecer um entendimento geral sobre como os papéis são geralmente mapeados para uma conta Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Fabricante de chips fornece serviços de segurança

Neste cenário, o fabricante gere a segurança para clientes de nível 1. Este cenário pode ser preferido por estes clientes de nível um, uma vez que não têm de gerir a segurança detalhada. 

O fabricante introduz segurança em Módulos de Segurança de Hardware (HSMs). Esta garantia pode incluir o fabricante que obtém chaves, certificados, etc. de potenciais clientes que já tenham casos DPS e configuração de grupos de inscrição. O fabricante também poderia gerar esta informação de segurança para os seus clientes.

Neste cenário, pode haver duas contas Azure envolvidas:

- **#1 conta**: Provavelmente partilhado entre as funções do operador e do desenvolvedor em certa medida. Esta parte pode adquirir as fichas HSM ao fabricante. Estes chips são apontados para casos de DPS associados à Conta #1. Com as inscrições em DPS, esta parte pode alugar dispositivos a vários clientes de nível dois, reconfigurando as definições de inscrição do dispositivo em DPS. Esta parte também pode ter hubs IoT atribuídos para sistemas de backend do utilizador final para interagir com para aceder à telemetria do dispositivo, etc. Neste último caso, pode não ser necessária uma segunda conta.

- **Conta #2**: Os utilizadores finais, os clientes de nível dois podem ter os seus próprios hubs IoT. A parte associada à Conta #1 apenas aponta dispositivos alugados para o centro correto nesta conta. Esta configuração requer a ligação de hubs DPS e IoT através das contas Azure, o que pode ser feito com modelos de Gestor de Recursos Azure.

#### <a name="all-in-one-oem"></a>OEM tudo-em-um

O fabricante poderia ser um "OEM All-in-one" onde apenas seria necessária uma única conta do fabricante. O fabricante trata da segurança e do fornecimento de ponta a ponta.

O fabricante pode fornecer uma aplicação baseada na nuvem para os clientes que adquirem dispositivos. Esta aplicação interagiria com o IoT Hub atribuído pelo fabricante.

Máquinas de venda automática ou máquinas de café automatizadas representam exemplos para este cenário.




## <a name="next-steps"></a>Passos seguintes

Poderá achar útil marcar este artigo como um ponto de referência, uma vez que trabalha através dos quickstarts de fornecimento automático correspondentes. 

Comece por completar um Quickstart "Configurar automaticamente" que melhor se adapte à sua preferência de ferramenta de gestão, o que o acompanha através da fase de "configuração de serviço":

- [Configurar o provisionamento automático utilizando o Azure CLI](quick-setup-auto-provision-cli.md)
- [Configurar o provisionamento automático utilizando o portal Azure](quick-setup-auto-provision.md)
- [Configurar o provisionamento automático usando um modelo de Gestor de Recursos](quick-setup-auto-provision-rm.md)

Em seguida, continue com um Quickstart "Provision a device" que se adequa ao mecanismo de atestado do seu dispositivo e à preferência SDK/idioma do Serviço de Provisionamento de Dispositivos. Neste Quickstart, você anda pelas fases "Inscrição do dispositivo" e "Registo e configuração do dispositivo": 

| Mecanismo de atestação de dispositivos | Quickstart SDK/Idioma | 
| ------------------------------- | -------------------- |
| Symmetric key | [C](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| Certificado X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Módulo de plataforma fidedigno simulado (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




