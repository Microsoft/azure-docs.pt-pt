---
title: Tutorial - Crie uma app de monitorização contínua do paciente com a Azure IoT Central | Microsoft Docs
description: Neste tutorial, você aprende a construir uma aplicação de monitorização contínua do paciente usando modelos de aplicação Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 56ff43980aafc75d5936b86c6ba2cd400311c5fa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719105"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação de monitorização contínua do paciente

Este tutorial mostra-lhe, como um construtor de soluções, como começar com a implementação de um modelo de aplicação de monitorização contínua do paciente IoT Central. Aprende-se a implementar e a utilizar o modelo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um modelo de aplicação
> * Caminhe através do modelo de aplicação

## <a name="prerequisites"></a>Pré-requisitos

Recomenda-se uma subscrição do Azure. Em alternativa, pode usar um teste gratuito de 7 dias. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application-template"></a>Criar um modelo de aplicação

Navegue para o site do [gestor de aplicações Azure IoT Central](https://apps.azureiotcentral.com/). **Selecione Construir** a partir da barra de navegação à esquerda e, em seguida, selecione o **separador Healthcare.**

:::image type="content" source="media/app-manager-health.png" alt-text="Modelo de aplicativo Healthcre":::

Selecione o botão **de aplicação Create** para começar a criar a sua aplicação e, em seguida, inicie sessão com uma conta pessoal, de trabalho ou escola da Microsoft. Vai levá-lo à nova página **de candidaturas.**

![Criar aplicação Cuidados de Saúde](media/app-manager-health-create.png)

![Criar informações de faturação de cuidados de saúde de aplicação](media/app-manager-health-create-billinginfo.png)

Para criar a sua aplicação:

1. O Azure IoT Central sugere automaticamente um nome de aplicação baseado no modelo que selecionou. Pode aceitar este nome ou introduzir o seu próprio nome de aplicação amigável, como **monitorização contínua do paciente.** Azure IoT Central também gera um prefixo URL único para si com base no nome da aplicação. Você é livre de mudar este prefixo URL para algo mais memorável, se quiser.

2. Pode selecionar se pretende criar a aplicação utilizando o plano de preços *gratuitos* ou um dos planos de preços *padrão.* As aplicações que cria usando o plano gratuito são gratuitas durante sete dias antes de expirarem e permitem até cinco dispositivos gratuitos. Pode mover uma aplicação do plano gratuito para um plano de preços padrão a qualquer momento antes de expirar. Se escolher o plano gratuito, tem de introduzir as suas informações de contacto e escolher se deve receber informações e dicas da Microsoft. As aplicações que cria utilizando um suporte de plano padrão até dois dispositivos gratuitos e exigem que introduza as informações de subscrição do Azure para faturação.

3. Selecione **Criar** na parte inferior da página para implementar a sua aplicação.

## <a name="walk-through-the-application-template"></a>Caminhe através do modelo de aplicação

### <a name="dashboards"></a>Dashboards

Depois de implementar o modelo de aplicação, você pousará primeiro no **painel de monitorização lamna no paciente**. Lamna Healthcare é um sistema hospitalar fictício que contém dois hospitais: o Hospital Woodgrove e o Hospital Burkville. No painel de instrumentos do Woodgrove Hospital, pode:

* Consulte a telemetria do dispositivo e propriedades como o **nível** de bateria do seu dispositivo ou o seu estado **de conectividade.**

* Veja a **planta do piso** e a localização do dispositivo Smart Vitals Patch.

* **Reprovisionar** o Smart Vitals Patch para um novo paciente.

* Veja um exemplo de um painel de **provedor** que uma equipa de cuidados hospitalares pode ver para rastrear os seus pacientes.

* Altere o estado do **paciente** do seu dispositivo para indicar se o dispositivo está a ser utilizado para um cenário in-patient ou remoto.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Estado do doente":::

Também pode selecionar **Ir ao painel de instrumentos do paciente remoto** para ver o painel do operador do Hospital de Burkville. Este painel contém um conjunto semelhante de ações, telemetria e informação. Também pode ver vários dispositivos em uso e optar por **atualizar o firmware** em cada um.

:::image type="content" source="media/lamna-remote.png" alt-text="Painel de instrumentos do operador remoto":::

### <a name="device-templates"></a>Modelos de dispositivo

Se selecionar **modelos de dispositivo,** consulte os dois tipos de dispositivo no modelo:

* **Smart Vitals Patch**: Este dispositivo representa um patch que mede vários sinais vitais. É usado para monitorizar pacientes dentro e fora do hospital. Se selecionar o modelo, verá que o patch envia dados do dispositivo, tais como o nível da bateria e a temperatura do dispositivo, e dados de saúde do paciente, tais como a taxa respiratória e a pressão arterial.

* **Cinta de joelho** inteligente : Este dispositivo representa uma joelheira que os pacientes usam quando recuperam de uma cirurgia de substituição do joelho. Se selecionar este modelo, vê capacidades como dados do dispositivo, alcance de movimento e aceleração.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Modelo de patch inteligente":::

### <a name="device-groups"></a>Device groups

Utilize grupos de dispositivos para agrupar logicamente um conjunto de dispositivos e, em seguida, executar consultas a granel ou operações sobre eles.

Se selecionar o separador grupos de dispositivos, vê um grupo de dispositivo predefinido para cada modelo de dispositivo na aplicação. São também criados dois grupos adicionais de dispositivos de amostra **chamados dispositivos e dispositivos de provisão** **com firmware desatualizado**. Pode utilizar estes grupos de dispositivos de amostra como entradas para executar alguns dos [Jobs](#jobs) na aplicação.

### <a name="rules"></a>Regras

Se selecionar **Regras,** vê as três regras no modelo:

* **Temperatura da cinta elevada**: Esta regra dispara quando a temperatura do dispositivo da joelheira inteligente é superior a 95 &deg; F sobre uma janela de 5 minutos. Utilize esta regra para alertar o paciente e a equipa de cuidados e arrefecer o dispositivo remotamente.

* **Queda detetada**: Esta regra é acionada se for detetada uma queda de um paciente. Use esta regra para configurar uma ação para implantar uma equipa operacional para ajudar o paciente que caiu.

* **Bateria de remendação fraca**: Esta regra é acionada quando o nível da bateria do dispositivo fica abaixo de 10%. Utilize esta regra para acionar uma notificação ao paciente para carregar o seu dispositivo.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Regras":::

### <a name="jobs"></a>Tarefas

Os trabalhos permitem-lhe executar operações a granel num conjunto de dispositivos, utilizando grupos de [dispositivos](#device-groups) como entrada. O modelo de aplicação tem dois trabalhos de amostra que um operador pode executar:

* **Atualizar firmware de joelheira**: Este trabalho encontra dispositivos no grupo de **dispositivos Dispositivos com firmware desatualizado** e executa um comando para atualizar esses dispositivos para a versão mais recente do firmware. Este trabalho de amostra pressupõe que os dispositivos podem lidar com um comando **de atualização** e, em seguida, recolher os ficheiros de firmware da nuvem.  

* **Dispositivos de re-provisão**: Tem um conjunto de dispositivos que foram recentemente devolvidos ao hospital. Este trabalho encontra dispositivos no grupo de **dispositivos Dispositivos de Provisão** e executa um comando para re-provisá-los para o próximo conjunto de pacientes.

### <a name="devices"></a>Dispositivos

Selecione o **separador Dispositivos** e, em seguida, selecione uma instância da **joelheira inteligente**. Existem três pontos de vista para explorar informações sobre o dispositivo em particular que selecionou. Estas vistas são criadas e publicadas quando constrói o modelo do dispositivo para o seu dispositivo. portanto, estas vistas são consistentes em todos os dispositivos que você conecta ou simula.

A vista **do Dashboard** apresenta uma visão geral da telemetria orientada para o operador e propriedades do dispositivo.

O separador **Propriedades** permite-lhe editar propriedades na nuvem e ler/escrever propriedades do dispositivo.

O **separador Comandos** permite-lhe executar comandos no dispositivo.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Painel de joelheira":::

### <a name="data-export"></a>Exportação de dados

A exportação de dados permite exportar continuamente os dados do seu dispositivo para outros serviços da Azure, incluindo a [API Azure para fHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a aplicação visitando **as definições de Aplicação > administração** e clique em **Eliminar**.

:::image type="content" source="media/admin-delete.png" alt-text="Recursos arrumados":::

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a criar um dashboard de fornecedor que se conecta à sua aplicação IoT Central.

> [!div class="nextstepaction"]
> [Construir um painel de fornecedores](tutorial-health-data-triage.md)