---
title: Criar uma app de monitorização contínua do paciente com a Azure IoT Central Microsoft Docs
description: Aprenda a construir uma aplicação de monitorização contínua do paciente utilizando modelos de aplicação Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 704c56745ad89e9ed2f79e8a863f1d0bc9845bf9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001830"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação de monitorização contínua do paciente



Este tutorial mostra-lhe, como um construtor de soluções, como começar com a implementação de um modelo de aplicação de monitorização contínua do paciente IoT Central. Você vai aprender a implementar o modelo, o que está incluído fora da caixa, e o que você pode fazer a seguir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um modelo de aplicação
> * Caminhe através do modelo de aplicação

## <a name="create-an-application-template"></a>Criar um modelo de aplicação

Navegue para o site do [gestor de aplicações Azure IoT Central](https://apps.azureiotcentral.com/). **Selecione Construir** a partir da barra de navegação à esquerda e, em seguida, clique no **separador Saúde.** 

>[!div class="mx-imgBorder"] 
>![Gestor de aplicativos Healthcare](media/app-manager-health.png)

Clique no botão **de aplicação Create** para começar a criar a sua aplicação e, em seguida, inicie sessão com uma conta pessoal, de trabalho ou escola da Microsoft. Vai levá-lo à nova página **de candidaturas.**

![Criar aplicação Cuidados de Saúde](media/app-manager-health-create.png)

![Criar informações de faturação de cuidados de saúde de aplicação](media/app-manager-health-create-billinginfo.png)

Para criar a sua aplicação:

1. O Azure IoT Central sugere automaticamente um nome de aplicação baseado no modelo que selecionou. Pode aceitar este nome ou introduzir o seu próprio nome de aplicação amigável, como **monitorização contínua do paciente.** Azure IoT Central também gera um prefixo URL único para si com base no nome da aplicação. Você é livre de mudar este prefixo URL para algo mais memorável, se quiser.

2. Pode selecionar se pretende criar a aplicação utilizando o plano de preços *gratuitos* ou um dos planos de preços *padrão.* As aplicações que cria usando o plano gratuito são gratuitas durante sete dias antes de expirarem e permitem até cinco dispositivos gratuitos. Pode mover uma aplicação do plano gratuito para um plano de preços padrão a qualquer momento antes de expirar. Se escolher o plano gratuito, tem de introduzir as suas informações de contacto e escolher se deve receber informações e dicas da Microsoft. As aplicações que cria utilizando um suporte de plano padrão até dois dispositivos gratuitos e exigem que introduza as informações de subscrição do Azure para faturação.

3. Selecione **Criar** na parte inferior da página para implementar a sua aplicação.

## <a name="walk-through-the-application-template"></a>Caminhe através do modelo de aplicação

### <a name="dashboards"></a>Dashboards

Depois de implementar o modelo de aplicação, você pousará primeiro no **painel de monitorização lamna no paciente**. Lamna Healthcare é um sistema hospitalar fictício que contém dois hospitais: o Hospital Woodgrove e o Hospital Burkville. Neste painel de instrumentos do Woodgrove Hospital, você verá informações e telemetria sobre os dispositivos neste modelo, juntamente com um conjunto de comandos, empregos e ações que você pode tomar. Do painel de instrumentos pode:

* Consulte a telemetria do dispositivo e propriedades como o **nível** de bateria do seu dispositivo ou o seu estado **de conectividade.**

* Veja a **planta do piso** e a localização do dispositivo Smart Vitals Patch.

* **Reprovisionar** o Smart Vitals Patch para um novo paciente.

* Veja um exemplo de um painel de **provedor** que uma equipa de cuidados hospitalares pode ver para rastrear os seus pacientes.

* Altere o estado do **paciente** do seu dispositivo para indicar se o dispositivo está a ser utilizado para um cenário in-patient ou remoto.

>[!div class="mx-imgBorder"] 
>![Lamna em paciente](media/lamna-in-patient.png)

Também pode clicar em **Ir ao painel de instrumentos do paciente remoto** para ver o segundo painel de instrumentos do operador utilizado para o Hospital De Burkville. Este painel contém um conjunto semelhante de ações, telemetria e informação. Além disso, pode ver vários dispositivos a serem utilizados e ter a capacidade de **atualizar o firmware** em cada um.

>[!div class="mx-imgBorder"] 
>![Remoto Lamna](media/lamna-remote.png)

Em ambos os painéis, pode sempre ligar-se a esta documentação.

### <a name="device-templates"></a>Modelos de dispositivo

Se clicar no **separador modelos do Dispositivo,** verá que existem dois tipos de dispositivos diferentes que fazem parte do modelo:

* **Smart Vitals Patch**: Este dispositivo representa um patch que mede diferentes tipos de sinais vitais. Pode ser usado para monitorizar pacientes dentro e fora do hospital. Se clicar no modelo, verá que além de enviar dados do dispositivo, como o nível da bateria e a temperatura do dispositivo, o patch também está a enviar dados de saúde do paciente, como a taxa respiratória e a pressão arterial.

* **Cinta de joelho**inteligente : Este dispositivo representa uma joelheira que os pacientes podem usar quando se recuperam de uma cirurgia de substituição do joelho. Se clicar neste modelo, verá capacidades como a gama de movimentos e aceleração, além dos dados do dispositivo.

>[!div class="mx-imgBorder"] 
>![Modelo de dispositivo de patch de sinais de sinais vitais inteligentes](media/smart-vitals-device-template.png)

### <a name="device-groups"></a>Device groups 
Os grupos de dispositivos permitem-lhe, logicamente, agrupar um conjunto de dispositivos para, em seguida, realizar consultas ou operações a granel sobre eles. 

Se clicar no separador grupos de dispositivos, verá que criámos alguns grupos de dispositivos predefinidos para cada um dos modelos do dispositivo na aplicação. Note que também criámos dois grupos adicionais de dispositivos de amostra chamados "Dispositivos de Provisão" e "Dispositivos com firmware desatualizado". Usaremos estes grupos de dispositivos de amostra como entradas para executar alguns [Jobs.](#jobs)

### <a name="rules"></a>Regras

Ao saltar para o separador regras, verá três regras que existem no modelo de aplicação:

* **Temperatura da cinta elevada**: Esta regra é ativada quando a temperatura do dispositivo da joelheira inteligente é superior a 95 &deg; F sobre uma janela de 5 minutos. Pode usar esta regra para alertar o paciente e a equipa de cuidados e arrefecer o dispositivo remotamente.

* **Queda detetada**: Esta regra é ativada se for detetada uma queda de um paciente. Pode usar esta regra para configurar uma ação para implantar uma equipa operacional para ajudar o paciente que caiu.

* **Bateria de remendação fraca**: Esta regra é acionada quando o nível da bateria do dispositivo fica abaixo de 10%. Pode usar esta regra para desencadear uma notificação ao paciente para carregar o seu dispositivo.

>[!div class="mx-imgBorder"] 
>![Regra alta da temperatura do aparelho](media/brace-temp-rule.png)

### <a name="jobs"></a>Tarefas

Os trabalhos permitem-lhe executar operações a granel num conjunto de dispositivos, utilizando grupos de [dispositivos](#device-groups) como entrada. Semeamos o modelo de aplicação com dois trabalhos de amostra que um operador de solução pode precisar de executar em algum ponto do ciclo de vida dos dispositivos:
* **Atualizar o firmware da joelheira**: Este trabalho irá encontrar dispositivos no grupo de dispositivos 'dispositivos com firmware desatualizado' e executar um comando para atualizar esses dispositivos para a versão mais recente do firmware da joelheira. Este trabalho de amostra pressupõe que os dispositivos têm capacidades para receber um comando de 'actualização' e recolher diretamente os ficheiros de firmware da nuvem.  

* **Dispositivos de re-provisão**: Se tiver um conjunto de dispositivos que foram recentemente devolvidos ao hospital e precisar de ser re-a provisionado para o próximo conjunto de pacientes, pode executar este trabalho para atualizar em massa os seus dispositivos de provisão. Neste caso, estamos a retirar todos os dispositivos de um grupo de dispositivos chamados "dispositivos de provisão", e estamos a executar um comando para os "realo provisionar". 

### <a name="devices"></a>Dispositivos

Clique no **separador Dispositivos** e, em seguida, selecione uma instância da **joelheira inteligente**. Verá que existem três pontos de vista para explorar informações sobre o dispositivo em particular que selecionou. Estas vistas são criadas e publicadas ao construir o modelo de dispositivo para o seu dispositivo, o que significa que serão consistentes em todos os dispositivos que você conectar ou simular.

A vista **do Dashboard** apresenta uma visão geral da telemetria e das propriedades que vêm do dispositivo orientado para o operador.

O separador **Propriedades** permitir-lhe-á editar propriedades na nuvem e ler/escrever propriedades do dispositivo.

O separador **Comandos** permitir-lhe-á executar comandos que foram modelados como parte do modelo do dispositivo.

>[!div class="mx-imgBorder"] 
>![Vistas do joelho](media/knee-brace-dashboard.png)

### <a name="data-export"></a>Exportação de dados

A exportação de dados permite exportar continuamente os seus dados do dispositivo IoT Central para outros serviços Azure, incluindo a [API Azure para FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar a utilizar esta aplicação, elimine a aplicação visitando **as definições de Aplicação > administração** e clique em **Eliminar**.

>[!div class="mx-imgBorder"] 
>![Excluir app](media/admin-delete.png)

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a criar um dashboard de fornecedor que se conecta à sua aplicação IoT Central.

> [!div class="nextstepaction"]
> [Construir um painel de fornecedores](howto-health-data-triage.md)