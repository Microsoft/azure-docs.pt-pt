---
title: Serviço de Provisionamento de Dispositivos IoT Hub - Conceitos de fornecimento automático
description: Este artigo fornece uma visão geral conceptual das fases de fornecimento automático do dispositivo, utilizando o Serviço de Provisionamento de Dispositivos IoT (DPS), IoT Hub e SDKs clientes.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975334"
---
# <a name="auto-provisioning-concepts"></a>Conceitos de fornecimento automático

Tal como descrito na [visão geral,](about-iot-dps.md)o Serviço de Provisionamento de Dispositivos é um serviço de ajuda que permite o fornecimento just-in-time de dispositivos a um hub IoT, sem necessitar de intervenção humana. Após o fornecimento bem sucedido, os dispositivos conectam-se diretamente com o seu Hub IoT designado. Este processo é referido como fornecimento automático, e proporciona uma experiência de registo fora da caixa e de configuração inicial para dispositivos.

## <a name="overview"></a>Descrição geral

O fornecimento automático Azure IoT pode ser dividido em três fases:

1. **Configuração** do serviço - uma configuração única das instâncias do Serviço de Provisionamento de Dispositivos Hub Azure IoT e IoT Hub, estabelecendo-as e criando ligações entre eles.

   > [!NOTE]
   > Independentemente do tamanho da sua solução IoT, mesmo que planeie suportar milhões de dispositivos, esta é uma **configuração única.**

2. **Inscrição** do dispositivo - o processo de sensibilizar a instância do Serviço de Fornecimento de Dispositivos para os dispositivos que tentarão registar-se no futuro. [A inscrição](concepts-service.md#enrollment) é realizada configurando informações de identidade do dispositivo no serviço de provisionamento, como uma "inscrição individual" para um único dispositivo, ou uma "inscrição em grupo" para vários dispositivos. A identidade baseia-se no mecanismo de [atestado](concepts-security.md#attestation-mechanism) que o dispositivo foi concebido para utilizar, o que permite ao serviço de provisionamento atestar a autenticidade do dispositivo durante o registo:

   - **TPM**: configurado como uma "inscrição individual", a identidade do dispositivo baseia-se no ID de registo tpm e na chave de averbamento público. Dado que o TPM é uma [especificação,](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)o serviço apenas espera atestar de acordo com a especificação, independentemente da implementação do TPM (hardware ou software). Consulte [o fornecimento do dispositivo: Atestação](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) de identidade com TPM para obter detalhes sobre o atestado baseado em TPM. 

   - **X509**: configurado como uma "inscrição individual" ou "inscrição em grupo", a identidade do dispositivo baseia-se num certificado digital X.509, que é enviado para a inscrição como ficheiro .pem ou .cer.

   > [!IMPORTANT]  
   > Embora não seja um pré-requisito para a utilização de Serviços de Provisionamento de Dispositivos, recomendamos vivamente que o seu dispositivo utilize um Módulo de Segurança de Hardware (HSM) para armazenar informações sensíveis sobre a identidade do dispositivo, tais como chaves e certificados X.509.

3. **Registo e configuração** do dispositivo - iniciado no arranque por software de registo, que é construído utilizando um SDK cliente do Serviço de Provisionamento de Dispositivos adequado para o dispositivo e mecanismo de atestação. O software estabelece uma ligação ao serviço de provisionamento para autenticação do dispositivo e subsequente registo no IoT Hub. Após o registo bem sucedido, o dispositivo é fornecido com o seu ID único dispositivo IoT Hub e informações de conexão, permitindo-lhe puxar a sua configuração inicial e iniciar o processo de telemetria. Em ambientes de produção, esta fase pode ocorrer semanas ou meses após as duas fases anteriores.

## <a name="roles-and-operations"></a>Funções e operações

As fases discutidas na secção anterior podem abranger semanas ou meses, devido a realidades de produção como o tempo de fabrico, o transporte marítimo, o processo aduaneiro, etc. Além disso, podem abranger atividades em várias funções, dadas as várias entidades envolvidas. Esta secção analisa mais profundamente as várias funções e operações relacionadas com cada fase e, em seguida, ilustra o fluxo num diagrama de sequência. 

O fornecimento automático também impõe requisitos ao fabricante do dispositivo, específico para permitir o mecanismo de atestado. As operações de fabrico também podem ocorrer independentemente do calendário das fases de fornecimento automático, especialmente nos casos em que os novos dispositivos são adquiridos após a criação de um fornecimento automático.

Uma série de Quickstarts são fornecidos na tabela de conteúdos à esquerda, para ajudar a explicar o fornecimento automático através da experiência prática. Para facilitar/simplificar o processo de aprendizagem, o software é utilizado para simular um dispositivo físico para inscrição e registo. Alguns Quickstarts exigem que você cumpra operações para múltiplas funções, incluindo operações para funções inexistentes, devido à natureza simulada dos Quickstarts.

| Função | Operação | Descrição |
|------| --------- | ------------|
| Fabricante | Codificar url de identidade e registo | Com base no mecanismo de atestado utilizado, o fabricante é responsável pela codificação das informações de identidade do dispositivo e do URL de registo do Serviço de Fornecimento de Dispositivos.<br><br>**Arranques rápidos**: uma vez que o dispositivo é simulado, não há nenhuma função do Fabricante. Consulte a função Developer para obter detalhes sobre como obtém esta informação, que é usada na codificação de uma aplicação de registo de amostras. |
| | Fornecer identidade do dispositivo | Como autor da informação de identidade do dispositivo, o fabricante é responsável pela sua comunicação ao operador (ou agente designado), ou pela sua inscrição direta no Serviço de Provisionamento de Dispositivos através de APIs.<br><br>**Arranques rápidos**: uma vez que o dispositivo é simulado, não há nenhuma função do Fabricante. Consulte a função Do Operador para obter a identidade do dispositivo, que é usada para inscrever um dispositivo simulado na sua instância de Serviço de Provisionamento de Dispositivos. |
| Operador | Configurar o fornecimento automático | Esta operação corresponde à primeira fase de fornecimento automático.<br><br>**Quickstarts**: Executa a função De Operador, configurando as instâncias do Serviço de Provisionamento de Dispositivos e Do Hub IoT na sua subscrição Azure. |
|  | Inscrever identidade do dispositivo | Esta operação corresponde à segunda fase de fornecimento automático.<br><br>**Arranques rápidos**: Executa a função De Operador, matriculando o seu dispositivo simulado na sua instância de Serviço de Provisionamento de Dispositivos. A identidade do dispositivo é determinada pelo método de atestado que está a ser simulado no Quickstart (TPM ou X.509). Consulte o papel de Developer para obter detalhes de atestação. |
| Serviço de Provisionamento de Dispositivos,<br>IoT Hub | \<todas as operações\> | Para uma implementação de produção com dispositivos físicos, e Quickstarts com dispositivos simulados, estas funções são cumpridas através dos serviços IoT que configura na sua subscrição Azure. As funções/operações funcionam exatamente da mesma forma, uma vez que os serviços IoT são indiferentes ao fornecimento de dispositivos físicos vs. simulados. |
| Developer (Programador) | Software de registo de construção/implantação | Esta operação corresponde à terceira fase de fornecimento automático. O Desenvolvedor é responsável pela construção e implementação do software de registo para o dispositivo, utilizando o SDK apropriado.<br><br>**Quickstarts**: A aplicação de registo de amostras que constrói simula um dispositivo real, para a sua plataforma/linguagem de eleição, que funciona na sua estação de trabalho (em vez de a implementar num dispositivo físico). A aplicação de registo realiza as mesmas operações que uma implantada num dispositivo físico. Especifica o método do atestado (certificado TPM ou X.509), além do URL de registo e do "ID Scope" da sua instância de Serviço de Provisionamento de Dispositivos. A identidade do dispositivo é determinada pela lógica de atestação SDK no prazo de execução, com base no método que especifica: <ul><li>**Atestado TPM** - a sua estação de trabalho de desenvolvimento executa uma [aplicação de simulador TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Uma vez em execução, uma aplicação separada é usada para extrair a "Chave de Averbamento" e o "ID de Registo" do TPM para utilização na inscrição da identidade do dispositivo. A lógica de atestação SDK também utiliza o simulador durante o registo, para apresentar um sinal sas assinado para verificação de autenticação e inscrição.</li><li>**Atestado X509** - usa uma ferramenta para [gerar um certificado](how-to-use-sdk-tools.md#x509-certificate-generator). Uma vez gerado, cria-se o ficheiro de certificado necessário para utilização na inscrição. A lógica de atestação SDK também utiliza o certificado durante o registo, para apresentar para autenticação e verificação de inscrições.</li></ul> |
| Dispositivo | Arranque e registo | Esta operação corresponde à terceira fase de fornecimento automático, preenchida pelo software de registo do dispositivo construído pelo Programador. Consulte o papel de Developer para obter mais detalhes. Após a primeira bota: <ol><li>A aplicação liga-se à instância do Serviço de Provisionamento de Dispositivos, de acordo com o URL global e o serviço "ID Scope" especificado durante o desenvolvimento.</li><li>Uma vez ligado, o dispositivo é autenticado contra o método de atestado e identidade especificado durante a inscrição.</li><li>Uma vez autenticado, o dispositivo é registado com a instância IoT Hub especificada pela instância de serviço de provisionamento.</li><li>Após o registo bem sucedido, um ponto final exclusivo do id do dispositivo e do IoT Hub são devolvidos ao pedido de registo para comunicação com o IoT Hub.</li><li> A partir daí, o dispositivo pode puxar para baixo o seu dispositivo inicial [de estado gémeo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) para configuração, e iniciar o processo de reportar dados de telemetria.</li></ol>**Arranques rápidos**: uma vez que o dispositivo é simulado, o software de registo funciona na sua estação de trabalho de desenvolvimento.|

O diagrama seguinte resume as funções e a sequenciação das operações durante o fornecimento automático do dispositivo:
<br><br>
[![Sequência de fornecimento automático para um dispositivo](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcionalmente, o fabricante também pode executar a operação "Inscrição identidade do dispositivo" utilizando APIs do Serviço de Provisionamento de Dispositivos (em vez de através do Operador). Para uma discussão detalhada sobre esta sequenciação e muito mais, consulte o registo do [dispositivo de toque Zero com vídeo Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (a partir do marcador 41:00)

## <a name="roles-and-azure-accounts"></a>Funções e contas Azure

A forma como cada papel é mapeado para uma conta Azure é dependente de cenários, e há alguns cenários que podem estar envolvidos. Os padrões comuns abaixo devem ajudar a fornecer um entendimento geral sobre como as funções são geralmente mapeadas para uma conta Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Fabricante de chips fornece serviços de segurança

Neste cenário, o fabricante gere a segurança dos clientes de nível 1. Este cenário pode ser preferido por estes clientes de nível um, uma vez que não têm de gerir a segurança detalhada. 

O fabricante introduz segurança em Módulos de Segurança de Hardware (HSMs). Esta garantia pode incluir o fabricante que obtém chaves, certificados, etc. de potenciais clientes que já tenham instâncias DPS e grupos de matrículas configurados. O fabricante também poderia gerar esta informação de segurança para os seus clientes.

Neste cenário, pode haver duas contas Azure envolvidas:

- **Conta #1**: Provavelmente partilhada em funções de operador e desenvolvedor de alguma forma. Esta parte pode comprar os chips HSM ao fabricante. Estes chips são apontados para casos de DPS associados à conta #1. Com as matrículas do DPS, esta parte pode alugar dispositivos a vários clientes de nível dois, reconfigurando as definições de inscrição do dispositivo em DPS. Esta parte também pode ter hubs IoT atribuídos para sistemas de backend de utilizador final para interface com para aceder à telemetria do dispositivo, etc. Neste último caso, não pode ser necessária uma segunda conta.

- **Conta #2**: Utilizadores finais, os clientes de nível dois podem ter os seus próprios hubs IoT. O partido associado à Conta #1 apenas aponta dispositivos alugados para o centro correto nesta conta. Esta configuração requer a ligação dos hubs DPS e IoT através das contas Azure, o que pode ser feito com os modelos do Gestor de Recursos Azure.

#### <a name="all-in-one-oem"></a>All-in-one OEM

O fabricante poderia ser um "OEM all-in-one" onde só seria necessária uma única conta de fabricante. O fabricante trata da segurança e do fornecimento de ponta a ponta.

O fabricante pode fornecer uma aplicação baseada na nuvem aos clientes que adquirem dispositivos. Esta aplicação interagiria com o Hub IoT atribuído pelo fabricante.

Máquinas de venda automática ou máquinas de café automatizadas representam exemplos para este cenário.




## <a name="next-steps"></a>Passos seguintes

Você pode achar útil reservar este artigo como um ponto de referência, uma vez que você trabalha o seu caminho através dos quickstarts de fornecimento automático correspondentes. 

Comece por completar um Quickstart "Configurar automaticamente" que melhor se adapte à sua preferência de ferramenta de gestão, que o acompanha através da fase de "Configuração de Serviço":

- [Configurar o fornecimento automático utilizando o Azure CLI](quick-setup-auto-provision-cli.md)
- [Configurar o fornecimento automático utilizando o portal Azure](quick-setup-auto-provision.md)
- [Configurar o fornecimento automático usando um modelo de Gestor de Recursos](quick-setup-auto-provision-rm.md)

Em seguida, continue com um "auto-provision um dispositivo simulado" Quickstart que se adequa ao mecanismo de atestado do dispositivo e à preferência do Serviço de Fornecimento de Dispositivos SDK/idioma. Neste Quickstart, você anda pelas fases de "Inscrição e configuração do dispositivo": 

|  | Mecanismo de atestado de dispositivo simulado | Quickstart SDK/Language |  |
|--|--|--|--|
|  | TPM (Trusted Platform Module) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C #](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certificado X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C #](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




