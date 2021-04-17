---
title: Gerencie os dispositivos na sua aplicação Azure IoT Central | Microsoft Docs
description: Como operador, aprenda a gerir dispositivos na sua aplicação Azure IoT Central. Saiba como gerir dispositivos individuais e fazer a importação e exportação a granel dos dispositivos na sua aplicação.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2ea75adfb7c2d990cfa543270f245113e15e4ee2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389847"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerir dispositivos na sua aplicação Azure IoT Central

Este artigo descreve como, como operador, gere dispositivos na sua aplicação Azure IoT Central. Como operador, pode:

- Utilize a página **dispositivos** para visualizar, adicionar e eliminar dispositivos ligados à sua aplicação Azure IoT Central.
- Dispositivos de importação e exportação a granel.
- Mantenha um inventário atualizado dos seus dispositivos.
- Mantenha os metadados do seu dispositivo atualizados alterando os valores armazenados nas propriedades do dispositivo a partir das suas vistas.
- Controle o comportamento dos seus dispositivos atualizando uma definição num dispositivo específico a partir das suas opiniões.

Para aprender a gerir grupos personalizados de dispositivos, consulte [Tutorial: Utilize grupos de dispositivos para analisar a telemetria do dispositivo](tutorial-use-device-groups.md).

## <a name="view-your-devices"></a>Ver os seus dispositivos

Para visualizar um dispositivo individual:

1. Escolha **dispositivos** no painel esquerdo. Aqui vê uma lista de todos os dispositivos e dos seus modelos de dispositivo.

1. Escolha um modelo de dispositivo.

1. No painel direito da página **Dispositivos,** vê-se uma lista de dispositivos criados a partir desse modelo do dispositivo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para este dispositivo:

    ![Página de detalhes do dispositivo](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo à sua aplicação Azure IoT Central:

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo a partir do qual pretende criar um dispositivo.

1. Escolha + **Novo**.

1. Ligue o toggle **simulado** para **ligar** ou **desligar**. Um dispositivo real é para um dispositivo físico que você liga à sua aplicação Azure IoT Central. Um dispositivo simulado tem dados de amostras gerados por Azure IoT Central.

1. Selecione **Criar**.

1. Este dispositivo aparece agora na sua lista de dispositivos para este modelo. Selecione o dispositivo para ver a página de detalhes do dispositivo que contém todas as visualizações para o dispositivo.

## <a name="import-devices"></a>Dispositivos de importação

Para ligar um grande número de dispositivos à sua aplicação, pode importar a granel dispositivos a partir de um ficheiro CSV. Pode encontrar um ficheiro CSV de exemplo no [repositório de Amostras Azure](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/bulk-upload-devices). O ficheiro CSV deve incluir os seguintes cabeçalhos de coluna:

| Coluna | Descrição 
| - | - | 
| IOTC_DEVICEID | O ID do dispositivo é um dispositivo único identificado que este dispositivo irá utilizar para se ligar. O ID do dispositivo pode conter letras, números e o `-` personagem sem espaços. |
| IOTC_DEVICENAME | Opcional. O nome do dispositivo é um nome amigável que será exibido ao longo da aplicação. Se não for especificado, este será o mesmo que o ID do dispositivo.   |



Para os dispositivos de registo a granel na sua aplicação:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo do dispositivo para o qual pretende criar em massa os dispositivos.

    > [!NOTE]
    > Se ainda não tiver um modelo de dispositivo, poderá importar dispositivos em todos os dispositivos e **registá-los** sem um modelo. Depois de importados os dispositivos, pode em seguida emigrá-los para um modelo.

1. Selecione **Import** (Importar).

    ![Ação de Importação](./media/howto-manage-devices/bulkimport1a.png)


1. Selecione o ficheiro CSV que tem a lista de IDs do dispositivo a ser importado.

1. A importação do dispositivo começa assim que o ficheiro tiver sido carregado. Pode acompanhar o estado de importação no painel operações do dispositivo. Este painel aparece automaticamente após o início da importação ou pode acessá-lo através do ícone da campainha no canto superior direito.

1. Uma vez concluída a importação, uma mensagem de sucesso é mostrada no painel de Operações do Dispositivo.

    ![Sucesso de Importação](./media/howto-manage-devices/bulkimport3a.png)

Se a operação de importação do dispositivo falhar, vê uma mensagem de erro no painel de Operações do Dispositivo. Um ficheiro de registo que captura todos os erros é gerado que pode descarregar.

## <a name="migrate-devices-to-a-template"></a>Migrar dispositivos para um modelo

Se registar os dispositivos iniciando a importação em **Todos os dispositivos,** os dispositivos são criados sem qualquer associação de modelos de dispositivo. Os dispositivos devem ser associados a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estes passos para associar os dispositivos a um modelo:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha **Todos os dispositivos:**

    ![Dispositivos não associados](./media/howto-manage-devices/unassociateddevices1a.png)

1. Utilize o filtro na grelha para determinar se o valor na coluna **do modelo** do dispositivo não está **ligado** a qualquer um dos seus dispositivos.

1. Selecione os dispositivos que pretende associar a um modelo:

1. **Selecione Migrar:**

    ![Dispositivos Associados](./media/howto-manage-devices/unassociateddevices2a.png)

1. Escolha o modelo na lista de modelos disponíveis e **selecione Migrar.**

1. Os dispositivos selecionados estão associados ao modelo de dispositivo que escolheu.

## <a name="export-devices"></a>Dispositivos de exportação

Para ligar um dispositivo real à IoT Central, precisa da sua cadeia de ligação. Pode exportar detalhes do dispositivo a granel para obter as informações necessárias para criar cordas de ligação do dispositivo. O processo de exportação cria um ficheiro CSV com a identidade do dispositivo, nome do dispositivo e chaves para todos os dispositivos selecionados.

Para dispositivos de exportação a granel da sua aplicação:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo do dispositivo a partir do qual pretende exportar os dispositivos.

1. Selecione os dispositivos que pretende exportar e, em seguida, selecione a ação **Exportação.**

    ![Exportar](./media/howto-manage-devices/export1a.png)

1. O processo de exportação começa. Pode rastrear o estado utilizando o painel de Operações do Dispositivo.

1. Quando a exportação termina, uma mensagem de sucesso é mostrada juntamente com um link para descarregar o ficheiro gerado.

1. Selecione o link **'Ficheiro de descarregamento'** para descarregar o ficheiro para uma pasta local no disco.

    ![Sucesso das Exportações](./media/howto-manage-devices/export2a.png)

1. O ficheiro CSV exportado contém as seguintes colunas: ID do dispositivo, nome do dispositivo, chaves do dispositivo e impressões digitais de certificado X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Para obter mais informações sobre as cordas de ligação e a ligação de dispositivos reais à sua aplicação IoT Central, consulte [a conectividade do dispositivo no Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Eliminar um dispositivo

Para eliminar um dispositivo real ou simulado da sua aplicação Azure IoT Central:

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo que pretende eliminar.

1. Utilize as ferramentas de filtro para filtrar e procurar os seus dispositivos. Verifique a caixa ao lado dos dispositivos para apagar.

1. Escolha **Eliminar**. Pode acompanhar o estado desta eliminação no painel de Operações do Dispositivo.

## <a name="change-a-property"></a>Alterar um imóvel

As propriedades da nuvem são os metadados do dispositivo associados ao dispositivo, como o número de cidade e de série. As propriedades em nuvem só existem na aplicação IoT Central e não estão sincronizadas com os seus dispositivos. As propriedades writable controlam o comportamento de um dispositivo e permitem definir o estado de um dispositivo remotamente, por exemplo, definindo a temperatura-alvo de um dispositivo termóstato.  As propriedades do dispositivo são definidas pelo dispositivo e são apenas de leitura dentro da IoT Central. Pode visualizar e atualizar propriedades nas **vistas de Detalhes** do Dispositivo para o seu dispositivo.

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades pretende alterar e selecione o dispositivo alvo.

1. Escolha a vista que contém propriedades para o seu dispositivo, esta vista permite-lhe inserir valores e selecione **Guardar** no topo da página. Aqui vê as propriedades que o seu dispositivo tem e os seus valores atuais. As propriedades em nuvem e as propriedades writable têm campos editáveis, enquanto as propriedades do dispositivo são apenas de leitura. Para propriedades writable, você pode ver o seu estado de sincronização na parte inferior do campo. 

1. Modifique as propriedades para os valores de que necessita. Pode modificar várias propriedades de cada vez e atualizá-las todas ao mesmo tempo.

1. Escolha **guardar**. Se guardar propriedades writable, os valores são enviados para o seu dispositivo. Quando o dispositivo confirmar a alteração da propriedade por si, o estado volta a **ser sincronizado**. Se guardou uma propriedade na nuvem, o valor é atualizado.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir dispositivos na sua aplicação Azure IoT Central, o próximo passo sugerido é aprender a[configurar regras](howto-configure-rules.md) para os seus dispositivos.
