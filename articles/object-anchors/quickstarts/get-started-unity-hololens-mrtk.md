---
title: 'Quickstart: Criar uma app HoloLens com Unidade e MRTK'
description: Neste quickstart, você aprende a construir uma aplicação HoloLens Unity usando MRTK e Object Anchors.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049750"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Quickstart: Criar uma app HoloLens com âncoras de objetos Azure, em Unidade com MRTK

Neste arranque rápido, você vai criar uma aplicação Unity HoloLens que usa [Âncoras de Objeto Azure](../overview.md). Azure Object Anchors é um serviço de nuvem gerido que converte ativos 3D em modelos de IA que permitem experiências de realidade mista conscientes de objetos para os HoloLens. Quando terminar, terá uma aplicação HoloLens construída com Unidade que pode detetar objetos no mundo físico.

Vai aprender a:

> [!div class="checklist"]
> * Prepare as definições de construção da Unidade.
> * Exportar o projeto HoloLens Visual Studio.
> * Implemente a aplicação e execute-a num dispositivo HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

In Unitity, abra o `quickstarts/apps/unity/mrtk` projeto.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Quando um diálogo "TMP Importer" lhe pedir para importar recursos textMesh Pro, selecione "Import TMP Essentials" para o fazer.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Importar Recursos TextMesh Pro":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Ligue o dispositivo, selecione **Todas as Aplicações** e, em seguida, localize e inicie a aplicação. Depois do ecrã de respingo da Unidade, deve ver uma caixa de delimitação branca. Pode usar a mão para mover, escalar ou rodar a caixa de delimitação. Coloque a caixa para cobrir o objeto que pretende detetar.

Abra o <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu</a> manual e selecione **Lock SearchArea** para evitar o movimento posterior da caixa de delimitação. Selecione **'Iniciar' procurar** para iniciar a deteção de objetos. Quando o objeto for detetado, uma malha será feita no objeto. Os detalhes de uma instância detetada aparecerão no ecrã, tais como o tempotamp e a relação de cobertura de superfície atualizada. Selecione **Stop Search** para parar de rastrear e todas as instâncias detetadas serão removidas.

#### <a name="the-app-menus"></a>Os menus de aplicativos

Também pode fazer outras ações utilizando o <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu de mãos.</a>

##### <a name="primary-menu"></a>Menu primário

* **Iniciar pesquisa / parar a pesquisa** – Inicia ou para o processo de deteção do objeto.
* **Toggle Mapear Espacial** – Mostrar/esconder a renderização do mapeamento espacial. Esta opção pode ser usada para depurar se a varredura estiver completa ou não.
* **Definições do rastreador** – Alterna a ativação do menu de definições do rastreador.
* **Configurações da Área de Pesquisa** – Alterna a ativação do menu de definições da área de pesquisa.
* **Iniciar o Rastreio** – Capturar dados de diagnóstico e guardá-los para o dispositivo. Consulte mais detalhes na secção **Debug Detection Issues and Capture Diagnostics**.
* **Upload Tracing** – Carregar dados de diagnóstico para o serviço 'Âncoras de Objectos'. Um utilizador deve fornecer a sua conta de subscrição `subscription.json` e carregá-la para a `LocalState` pasta. Um ficheiro de amostra `subscription.json` pode ser encontrado abaixo.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Menu de mão primária de unidade":::

##### <a name="tracker-settings-menu"></a>Menu de definições de rastreador

* **Alta Precisão** – Uma característica experimental usada para obter uma pose mais precisa. Ativar esta opção exigirá mais recursos do sistema durante a deteção de objetos. A malha do objeto será renderizada em rosa quando estiver neste modo. Selecione este botão novamente para voltar ao modo de rastreio normal.
* **Alinhamento Vertical Descontraído** – Quando ativado, permite que um objeto seja detetado num ângulo não vertical. Útil para detetar objetos nas rampas.
* **Permitir a alteração da escala** – Permite que o rastreador altere o tamanho do objeto detetado com base em informações ambientais.
* **Rácio de Cobertura Slider** – Ajusta a proporção de pontos de superfície que devem corresponder para o rastreador detetar um objeto.  Valores mais baixos permitem que o rastreador detete melhor objetos que são desafiantes para os sensores HoloLens detetarem, tais como objetos escuros ou objetos altamente reflexivos. Valores mais elevados reduzirão a frequência de deteções falsas.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Menu de mão de rastreador de unidade":::

##### <a name="search-area-settings-menu"></a>Menu de definições de Área de Pesquisa

* **Bloquear área de busca** – Bloquear a caixa de encadernação da área para evitar movimentos acidentais por mãos.
* **Ajustar automaticamente a Área de Pesquisa** – Permite que a área de pesquisa se reposicione durante a deteção de objetos.
* **Malha de ciclo** – Ciclo através da visualização das malhas carregadas dentro da área de pesquisa.  Esta opção pode ajudar os utilizadores a alinhar a caixa de pesquisa para detetar objetos difíceis de detetar.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Menu de mão de área de pesquisa de unidade":::

`subscription.json`Exemplo:

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conceitos: Visão geral do SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)
