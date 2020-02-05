---
title: Criar uma aplicação contínua de monitorização do paciente com a Central Azure IoT  Microsoft Docs
description: Aprenda a construir uma aplicação contínua de monitorização do paciente utilizando modelos de aplicação Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: c9a153cc5b1b8c43a42b37d591a8946a545f63ff
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986439"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Tutorial: Implementar e passar por um modelo contínuo de aplicação de monitorização do paciente

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra-lhe, como construtor de soluções, como começar com a implementação de um modelo de aplicação contínua de monitorização contínua do paciente IoT Central. Você vai aprender a implementar o modelo, o que está incluído fora da caixa, e o que você pode querer fazer a seguir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um modelo de aplicação
> * Caminhe pelo modelo de aplicação

## <a name="create-an-application-template"></a>Criar um modelo de aplicação

Navegue para o site do gestor de [aplicações Azure IoT Central.](https://apps.azureiotcentral.com/) Selecione **Construir** a partir da barra de navegação à esquerda e, em seguida, clique no separador Cuidados de **Saúde.** 

>[!div class="mx-imgBorder"] 
>![gestor de aplicativos Healthcare](media/app-manager-health.png)

Clique no botão **de aplicação Create** para começar a criar a sua aplicação e, em seguida, iniciar sessão com uma conta pessoal, de trabalho ou escolar da Microsoft. Isto leva-o à página de **aplicação Nova.**

![Criar aplicação Cuidados de Saúde](media/app-manager-health-create.png)

![Criar informação sobre faturação de cuidados de saúde de aplicação](media/app-manager-health-create-billinginfo.png)

Para criar a sua aplicação:

1. A Azure IoT Central sugere automaticamente um nome de aplicação baseado no modelo que selecionou. Pode aceitar este nome ou inserir o seu próprio nome de candidatura amigável, como **monitorização contínua do paciente**. A Azure IoT Central também gera um prefixo URL único para si com base no nome da aplicação. Você está livre para alterar esse prefixo de URL para algo mais fácil de memorizar, se desejar.

2. Pode selecionar se pretende criar a aplicação utilizando o plano de preços *gratuitos* ou um dos planos de preços *padrão.* As aplicações que cria utilizando o plano gratuito são gratuitas durante sete dias antes de expirarem e permitem até cinco dispositivos gratuitos. Pode mover uma aplicação do plano livre para um plano de preços padrão a qualquer momento antes de expirar. Se escolher o plano gratuito, tem de introduzir as suas informações de contacto e escolher se recebe informações e dicas da Microsoft. As aplicações que cria utilizando um suporte de plano padrão até dois dispositivos gratuitos e exigem que introduza as informações de subscrição do Azure para faturação.

3. Selecione **Criar** na parte inferior da página para implementar a sua aplicação.

## <a name="walk-through-the-application-template"></a>Caminhe pelo modelo de aplicação

### <a name="dashboards"></a>Dashboards

Depois de implementar o modelo de aplicação, você irá primeiro aterrar no painel de **monitorização do paciente lamna**. Lamna Healthcare é um sistema hospitalar fictício que contém dois hospitais: O Hospital Woodgrove e o Hospital Burkville. Neste painel de telefonista do Hospital Woodgrove, você verá informações e telemetria sobre os dispositivos neste modelo juntamente com um conjunto de comandos, empregos e ações que você pode tomar. A partir do painel de instrumentos pode:

* Consulte a telemetria do dispositivo e propriedades como o **nível** de bateria do seu dispositivo ou o seu estado **de conectividade.**

* Veja a planta do **piso** e a localização do dispositivo Smart Vitals Patch.

* **Reabastecer** o Patch Smart Vitals para um novo paciente.

* Veja um exemplo de um painel de **fornecedores** que uma equipa de cuidados hospitalares pode ver para rastrear os seus pacientes.

* Altere o estado do **paciente** do seu dispositivo para indicar se o dispositivo está a ser utilizado para um cenário paciente ou remoto.

>[!div class="mx-imgBorder"] 
>![](media/lamna-in-patient.png) interna da Lamna

Também pode clicar em **Ir ao painel de instrumentos do paciente remoto** para ver o segundo painel de instrumentos do operador utilizado para o Hospital De Burkville. Este painel contém um conjunto semelhante de ações, telemetria e informação. Além disso, pode ver vários dispositivos a serem utilizados e ter a capacidade de **atualizar o firmware** em cada um.

>[!div class="mx-imgBorder"] 
>![](media/lamna-remote.png) remoto lamna

Em ambos os dashboards pode sempre ligar-se a esta documentação.

### <a name="device-templates"></a>Modelos de dispositivo

Se clicar no separador de **modelos do Dispositivo,** verá que existem dois tipos diferentes de dispositivos que fazem parte do modelo:

* **Smart Vitals Patch**: Este dispositivo representa um patch que mede uma variedade de sinais vitais que poderiam ser usados para monitorizar pacientes dentro e fora do hospital. Se clicar no modelo, verá que além de enviar dados do dispositivo, como o nível da bateria e a temperatura do dispositivo, o patch também está a enviar dados de saúde do paciente, como a taxa respiratória e a pressão arterial.

* **Cinta inteligente do joelho**: Este dispositivo representa uma joelheira que os pacientes podem usar quando se recuperam de uma cirurgia de substituição do joelho. Se clicar neste modelo, verá capacidades como alcance de movimento e aceleração, além dos dados do dispositivo.

>[!div class="mx-imgBorder"] 
>![smart vitals patch dispositivo](media/smart-vitals-device-template.png)

Se clicar no separador de **grupos do Dispositivo,** verá também que estes modelos de dispositivo saem automaticamente de grupos de dispositivos criados para os mesmos.

### <a name="rules"></a>Regras

Ao saltar para o separador de regras, você verá três regras que existem no modelo de aplicação:

* **Temperatura da braçadeira alta**: Esta regra é ativada quando a temperatura do dispositivo da joelheira inteligente é superior a 95&deg;F numa janela de 5 minutos. Pode usar esta regra para alertar o paciente e a equipa de cuidados, e arrefecer o dispositivo remotamente.

* **Queda detetada**: Esta regra é desencadeada se for detetada uma queda do paciente. Pode usar esta regra para configurar uma ação para implantar uma equipa operacional para ajudar o paciente que caiu.

* **Bateria de remendo baixa**: Esta regra é ativada quando o nível da bateria no dispositivo fica abaixo de 10%. Pode usar esta regra para desencadear uma notificação ao paciente para carregar o seu dispositivo.

>[!div class="mx-imgBorder"] 
>![temperatura da cinta alta regra](media/brace-temp-rule.png)

### <a name="devices"></a>Dispositivos

Clique no separador **Dispositivos** e, em seguida, selecione uma instância da **joelheira inteligente**. Verá que existem três pontos de vista para poder explorar informações sobre o dispositivo específico que selecionou. Estas vistas são criadas e publicadas ao construir o modelo do dispositivo para o seu dispositivo. Isto significa que serão consistentes em todos os dispositivos que ligar ou simular.

A vista **Dashboard** dá uma visão geral da telemetria e das propriedades que vêm do dispositivo que são orientados para o operador.

O separador **Propriedades** permitirá editar propriedades na nuvem e ler/escrever propriedades do dispositivo.

O separador **Comandos** permitir-lhe-á executar comandos que tenham sido modelados como parte do modelo do seu dispositivo.

>[!div class="mx-imgBorder"] 
>![joelheira vê](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine a aplicação visitando as definições de **Administração > Aplicação** e clique em **Eliminar**.

>[!div class="mx-imgBorder"] 
>![Apagar](media/admin-delete.png) de aplicações

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a criar um dashboard fornecedor que se conecta à sua aplicação IoT Central.

> [!div class="nextstepaction"]
> [Construir um dashboard fornecedor](howto-health-data-triage.md)