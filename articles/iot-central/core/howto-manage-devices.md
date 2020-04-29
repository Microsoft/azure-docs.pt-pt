---
title: Gerencie os dispositivos na sua aplicação Azure IoT Central [ Microsoft Docs
description: Como operador, aprenda a gerir dispositivos na sua aplicação Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80157947"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerir dispositivos na sua aplicação Azure IoT Central



Este artigo descreve como, como operador, gerir dispositivos na sua aplicação Azure IoT Central. Como operador, pode:

- Utilize a página **Dispositivos** para visualizar, adicionar e eliminar dispositivos ligados à sua aplicação Azure IoT Central.
- Mantenha um inventário atualizado dos seus dispositivos.
- Mantenha os metadados do dispositivo atualizados alterando os valores armazenados nas propriedades do dispositivo a partir das suas visualizações.
- Controle o comportamento dos seus dispositivos atualizando uma definição num dispositivo específico a partir das suas opiniões.

## <a name="view-your-devices"></a>Ver os seus dispositivos

Para visualizar um dispositivo individual:

1. Escolha **os dispositivos** no painel esquerdo. Aqui você vê uma lista de todos os dispositivos e dos seus modelos de dispositivo.

1. Escolha um modelo de dispositivo.

1. No painel da direita da página **Dispositivos,** vê-se uma lista de dispositivos criados a partir desse modelo de dispositivo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para esse dispositivo:

    ![Página de detalhes do dispositivo](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo à sua aplicação Azure IoT Central:

1. Escolha **os dispositivos** no painel esquerdo.

1. Escolha o modelo do dispositivo a partir do qual pretende criar um dispositivo.

1. Escolha + **Novo**.

1. Rode o toggle **simulado** para **ligar** ou **desligar**. Um dispositivo real é para um dispositivo físico que você conecta à sua aplicação Azure IoT Central. Um dispositivo simulado tem dados de amostra gerados pela Azure IoT Central.

1. Clique em **Criar**.

1. Este dispositivo aparece agora na sua lista de dispositivos para este modelo. Selecione o dispositivo para ver a página de detalhes do dispositivo que contém todas as vistas para o dispositivo.

## <a name="import-devices"></a>Dispositivos de importação

Para ligar um grande número de dispositivos à sua aplicação, pode importar dispositivos a granel a partir de um ficheiro CSV. O ficheiro CSV deve ter as seguintes colunas e cabeçalhos:

* **IOTC_DeviceID** - o ID do dispositivo deve ser todo minúsculo.
* **IOTC_DeviceName** - esta coluna é opcional.

Para registar dispositivos a granel na sua aplicação:

1. Escolha **os dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo do dispositivo para o qual pretende criar os dispositivos a granel.

    > [!NOTE]
    > Se ainda não tiver um modelo de dispositivo, pode importar dispositivos em **todos os dispositivos** e registá-los sem um modelo. Depois de os dispositivos terem sido importados, pode emigrar para um modelo.

1. Selecione **Importar**.

    ![Ação de Importação](./media/howto-manage-devices/bulkimport1a.png)


1. Selecione o ficheiro CSV que tem a lista de IDs do Dispositivo a importar.

1. A importação do dispositivo começa assim que o ficheiro tiver sido carregado. Pode rastrear o estado de importação no painel operações do dispositivo. Este painel aparece automaticamente após o início da importação ou pode acessá-lo através do ícone do sino no canto superior direito.

1. Uma vez concluída a importação, uma mensagem de sucesso é mostrada no painel de Operações de Dispositivos.

    ![Sucesso da Importação](./media/howto-manage-devices/bulkimport3a.png)


Se a operação de importação do dispositivo falhar, verá uma mensagem de erro no painel de Operações do Dispositivo. É gerado um ficheiro de registo que captura todos os erros que pode descarregar.

**Dispositivos de migração para um modelo**

Se registar os dispositivos iniciando a importação em **todos os dispositivos,** os dispositivos são criados sem qualquer associação de modelos de dispositivo. Os dispositivos devem estar associados a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estes passos para associar dispositivos com um modelo:

1. Escolha **os dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha **Todos os dispositivos:**

    ![Dispositivos Não Associados](./media/howto-manage-devices/unassociateddevices1a.png)


1. Utilize o filtro na grelha para determinar se o valor na coluna do modelo do **dispositivo** é "não associado" para qualquer um dos seus dispositivos.

1. Selecione os dispositivos que pretende associar com um modelo:

1. **Selecione Migrar:**

    ![Dispositivos Associados](./media/howto-manage-devices/unassociateddevices2a.png)


1. Escolha o modelo da lista de modelos disponíveis e **selecione Migrar**.

1. Os dispositivos selecionados estão associados ao modelo de dispositivo que escolheu.


## <a name="export-devices"></a>Dispositivos de exportação

Para ligar um dispositivo real à IoT Central, precisa da corda de ligação. Pode exportar detalhes do dispositivo a granel para obter a informação necessária para criar cordas de ligação ao dispositivo. O processo de exportação cria um ficheiro CSV com a identidade do dispositivo, nome do dispositivo e chaves para todos os dispositivos selecionados.

Para dispositivos de exportação a granel da sua aplicação:

1. Escolha **os dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo do dispositivo a partir do qual pretende exportar os dispositivos.

1. Selecione os dispositivos que pretende exportar e, em seguida, selecione a ação **Export.**

    ![Exportar](./media/howto-manage-devices/export1a.png)


1. O processo de exportação começa. Pode rastrear o estado utilizando o painel operações do dispositivo.

1. Quando a exportação termina, uma mensagem de sucesso é mostrada juntamente com um link para descarregar o ficheiro gerado.

1. Selecione o link **Download File** para transferir o ficheiro para uma pasta local no disco.

    ![Sucesso da Exportação](./media/howto-manage-devices/export2a.png)


1. O ficheiro CSV exportado contém as seguintes colunas: ID do dispositivo, nome do dispositivo, chaves do dispositivo e impressões digitais do certificado X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Para obter mais informações sobre cordas de ligação e ligar dispositivos reais à sua aplicação IoT Central, consulte a [conectividade do Dispositivo em Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Eliminar um dispositivo

Para eliminar um dispositivo real ou simulado da sua aplicação Central Azure IoT:

1. Escolha **os dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo que pretende eliminar.

1. Utilize as ferramentas do filtro para filtrar e procurar os seus dispositivos. Verifique a caixa ao lado dos dispositivos para apagar.

1. Escolha **Eliminar**. Pode rastrear o estado desta eliminação no painel de Operações de Dispositivos.

## <a name="change-a-property"></a>Alterar um imóvel

As propriedades da nuvem são os metadados do dispositivo associados ao dispositivo, como cidade e número de série. Propriedades reempreensíveis controlam o comportamento de um dispositivo. Por outras palavras, permitem-lhe fornecer inputs ao seu dispositivo.  As propriedades do dispositivo são definidas pelo dispositivo e são apenas de leitura dentro da IoT Central. Pode visualizar e atualizar as propriedades nas vistas de Detalhes do **Dispositivo** para o seu dispositivo.

1. Escolha **os dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades pretende alterar e selecione o dispositivo-alvo.

1. Escolha a vista que contém propriedades para o seu dispositivo, esta vista permite-lhe inserir valores e selecionar **Guardar** na parte superior da página. Aqui vê as propriedades que o seu dispositivo tem e os seus valores atuais. As propriedades da nuvem e as propriedades reempreensíveis têm campos editáveis, enquanto as propriedades do dispositivo são apenas de leitura. Para propriedades reempreciáveis, pode ver o seu estado de sincronização na parte inferior do campo. 

1. Modifique as propriedades para os valores de que necessita. Pode modificar várias propriedades de cada vez e atualizá-las todas ao mesmo tempo.

1. Escolha **Guardar**. Se guardou propriedades reempoáveis, os valores são enviados para o seu dispositivo. Quando o dispositivo confirma a alteração da propriedade reempreciável, o estado volta a **sincronizar.** Se guardou uma propriedade em nuvem, o valor é atualizado.


## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir dispositivos na sua aplicação Azure IoT Central, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como utilizar grupos de dispositivos](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
