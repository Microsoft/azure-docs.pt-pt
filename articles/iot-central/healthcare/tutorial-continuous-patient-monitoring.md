---
title: Criar um aplicativo de monitoramento de pacientes contínuo com o Azure IoT Central | Microsoft Docs
description: Aprenda a criar um aplicativo de monitoramento contínuo do paciente usando os modelos de aplicativo IoT Central do Azure.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 97a215d8f111753c8fcc857fe4c48956c1236b3b
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027448"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Tutorial: implantar e Walkthrough um modelo de aplicativo de monitoramento contínuo do paciente

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra, como um Solution Builder, como começar implantando um modelo de aplicativo de monitoramento contínuo do paciente IoT Central. Você aprenderá a implantar o modelo, o que está incluído prontamente e o que você pode fazer em seguida.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um modelo de aplicativo
> * Percorrer o modelo de aplicativo

## <a name="create-an-application-template"></a>Criar um modelo de aplicativo

Navegue até o [site do Azure IOT central Application Manager](https://apps.azureiotcentral.com/). Selecione **criar** na barra de navegação à esquerda e, em seguida, clique na guia **assistência médica** . 

>[!div class="mx-imgBorder"] 
>](media/app-manager-health.png) de saúde do ![app Manager

Clique no botão **criar aplicativo** para começar a criar seu aplicativo e, em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft. Isso levará você para a página **novo aplicativo** .

>[!div class="mx-imgBorder"] 
>![criar](media/app-manager-health-create.png) de saúde do aplicativo

Para criar seu aplicativo:

1. O IoT Central do Azure sugere automaticamente um nome de aplicativo com base no modelo que você selecionou. Você pode aceitar esse nome ou inserir seu próprio nome de aplicativo amigável, como o **monitoramento contínuo do paciente**. O Azure IoT Central também gera um prefixo de URL exclusivo para você com base no nome do aplicativo. Você está livre para alterar esse prefixo de URL para algo mais fácil de memorizar, se desejar.

2. Você pode selecionar se deseja criar um aplicativo de **avaliação** ou um aplicativo **pago conforme o uso** . Os aplicativos de **avaliação** são gratuitos por sete dias antes de expirarem e permitem até cinco dispositivos livres. Podem ser convertidas em Pay As You Go a qualquer momento antes de expirarem. Se você criar um aplicativo de avaliação, precisará inserir suas informações de contato e escolher se deseja receber informações e dicas da Microsoft. Os aplicativos **pagos conforme o uso** dão suporte a até dois dispositivos gratuitos e exigem que você coloque suas informações de assinatura do Azure.

3. Selecione **criar** na parte inferior da página para implantar seu aplicativo.

## <a name="walk-through-the-application-template"></a>Percorrer o modelo de aplicativo

### <a name="dashboards"></a>Dashboards

Depois de implantar o modelo de aplicativo, você começará primeiro no **painel de monitoramento Lamna no paciente**. O Lamna Healthcare é um sistema de hospital fictício que contém dois hospitais: Woodgrove hospital e Burkville hospital. Neste painel do operador para o Woodgrove hospital, você verá informações e telemetria sobre os dispositivos neste modelo junto com um conjunto de comandos, trabalhos e ações que você pode tomar. No painel, você pode:

* Consulte telemetria e propriedades do dispositivo, como o **nível de bateria** do seu dispositivo ou seu status de **conectividade** .

* Exiba o **plano** e o local do andar do dispositivo de patch do Smart vitals.

* **Reprovisione** o patch dos vitals inteligentes para um novo paciente.

* Veja um exemplo de um **painel de provedor** que uma equipe de assistência do hospital pode ver para controlar seus pacientes.

* Altere o **status do paciente** do seu dispositivo para indicar se o dispositivo está sendo usado para um cenário de paciente ou remoto.

>[!div class="mx-imgBorder"] 
>![Lamna no paciente](media/lamna-in-patient.png)

Você também pode clicar em **ir para o painel remoto do paciente** para ver o segundo painel do operador usado para o Burkville hospital. Esse painel contém um conjunto semelhante de ações, telemetria e informações. Além disso, você pode ver vários dispositivos sendo usados e ter a capacidade de **atualizar o firmware** em cada um deles.

>[!div class="mx-imgBorder"] 
>](media/lamna-remote.png) remoto ![Lamna

Em ambos os painéis, você sempre pode vincular novamente a esta documentação.

### <a name="device-templates"></a>Modelos de dispositivo

Se você clicar na guia **modelos de dispositivo** , verá que há dois tipos diferentes de dispositivos que fazem parte do modelo:

* **Patch do Smart vitals**: este dispositivo representa um patch que mede uma variedade de sinais vitais que podem ser usados para monitorar pacientes dentro e fora do hospital. Se você clicar no modelo, verá que, além de enviar dados do dispositivo, como o nível da bateria e a temperatura do dispositivo, o patch também estará enviando dados de integridade do paciente, como taxa de respiratory e pressão de sangue.

* **Chave do Smart joelho**: este dispositivo representa uma chave de joelho que os pacientes podem usar ao recuperar de um cirurgia de substituição de joelho. Se você clicar nesse modelo, verá recursos como intervalo de movimento e aceleração, além dos dados do dispositivo.

>[!div class="mx-imgBorder"] 
>![modelo de dispositivo de patch dos vitals inteligentes](media/smart-vitals-device-template.png)

Se você clicar na guia **grupos de dispositivos** , verá também que esses modelos de dispositivo automaticamente têm grupos de dispositivos criados para eles.

### <a name="rules"></a>Regras

Ao saltar para a guia regras, você verá três regras que existem no modelo de aplicativo:

* **Temperatura alta de chave**: essa regra é disparada quando a temperatura do dispositivo da chave do Smart joelho é maior que 95&deg;F em uma janela de 5 minutos. Você pode usar essa regra para alertar a equipe de pacientes e cuidados e resfriar o dispositivo remotamente.

* **Outono detectado**: essa regra será disparada se um paciente de pacientes for detectado. Você pode usar essa regra para configurar uma ação para implantar uma equipe operacional para auxiliar o paciente que caiu.

* **Patch da bateria baixa**: essa regra é disparada quando o nível da bateria no dispositivo é inferior a 10%. Você pode usar essa regra para disparar uma notificação ao paciente para cobrar seu dispositivo.

>[!div class="mx-imgBorder"] 
>regra de ![de temperatura alta de chave](media/brace-temp-rule.png)

### <a name="devices"></a>Dispositivos

Clique na guia **dispositivos** e selecione uma instância da **chave joelho inteligente**. Você verá que há três exibições para poder explorar informações sobre o dispositivo específico que você selecionou. Essas exibições são criadas e publicadas ao criar o modelo de dispositivo para seu dispositivo. Isso significa que eles serão consistentes em todos os dispositivos que você conectar ou simular.

A exibição **painel** fornece uma visão geral da telemetria e das propriedades provenientes do dispositivo que são orientadas por telefonista.

A guia **Propriedades** permitirá que você edite Propriedades de nuvem e as propriedades do dispositivo de leitura/gravação.

A guia **comandos** permitirá que você execute comandos que foram modelados como parte do seu modelo de dispositivo.

>[!div class="mx-imgBorder"] 
>![exibições de chave joelho](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for continuar a usar este aplicativo, exclua o aplicativo visitando **administração > configurações do aplicativo** e clique em **excluir**.

>[!div class="mx-imgBorder"] 
>![excluir](media/admin-delete.png) de aplicativo

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para saber como criar um painel de provedor que se conecta ao seu aplicativo IoT Central.

> [!div class="nextstepaction"]
> [Criar um painel de provedor](howto-health-data-triage.md)