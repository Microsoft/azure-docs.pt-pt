---
title: Criar um registo de esquemas Azure Event Hubs
description: Este artigo mostra-lhe como criar um registo de esquemas num espaço de nomes Azure Event Hubs.
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 90556e0843cda94dc79330321d027f8d28eb7d20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652198"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Criar um registo de esquemas Azure Event Hubs (pré-visualização)
Este artigo mostra-lhe como criar um grupo de esquemas com esquemas num registo de esquemas hospedado pela Azure Event Hubs. Para uma visão geral da funcionalidade de Registo de Schema dos Hubs de Eventos Azure, consulte [o Registo Azure Schema em Centros de Eventos.](schema-registry-overview.md)

> [!NOTE]
> - A funcionalidade **de Registo de Schema** está atualmente em **pré-visualização,** e não é recomendada para cargas de trabalho de produção.
> - A funcionalidade está disponível apenas em níveis **standard** e **dedicados,** não no nível **básico.**

## <a name="prerequisites"></a>Pré-requisitos
[Crie um espaço de nomes de Centros de Eventos.](event-hubs-create.md#create-an-event-hubs-namespace) Também pode utilizar um espaço de nome existente. 

## <a name="create-a-schema-group"></a>Criar um grupo de esquemas
1. Navegue para a página **de Espaço nome de Centros de Eventos.** 
1. Selecione **Registo de Schema** no menu esquerdo. Para criar um grupo de esquemas, selecione **+ Grupo Schema** na barra de ferramentas. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Página do Registo de Schema":::
1. Na página do **Grupo Create Schema,** faça estes passos:
    1. Insira um **nome** para o grupo de esquemas.
    1. Para **o tipo de serialização,** escolha um formato de serialização que se aplique a todos os esquemas do grupo de esquemas. Atualmente, **a Avro** é o único tipo suportado, por isso selecione **Avro.** 
    1. Selecione um **modo de compatibilidade** para todos os esquemas do grupo. Para **o Avro,** os modos de compatibilidade para a frente e para trás são suportados. 
    1. Em seguida, **selecione Criar** para criar o grupo de esquemas. 
1. Selecione o nome do **grupo de esquemas** na lista de grupos de esquemas.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="Página do Registo de Schema":::    
1. Veja a página **do Grupo Schema** para o grupo.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Página do Registo de Schema":::
    

## <a name="add-a-schema-to-the-schema-group"></a>Adicione um esquema ao grupo de esquemas
Nesta secção, adicione um esquema ao grupo de esquemas utilizando o portal Azure. 

1. Na página **do Grupo Schema,** selecione **+ Schema** na barra de ferramentas. 
1. Na página **Create Schema,** faça estes passos:
    1. Insira um **nome** para o esquema.
    1. Introduza o seguinte **esquema** na caixa de texto. Também pode selecionar ficheiro com o esquema.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. Selecione **Criar**. 
1. Selecione o **esquema** na lista de esquemas. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Página do Registo de Schema":::
1. Vê a página do Esquema de **Schema.** 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Página do Registo de Schema":::    
1. Se houver várias versões de um esquema, vê-las na lista de lançamentos de **versões.** Selecione uma versão para mudar para o esquema de versão. 

## <a name="create-a-new-version-of-schema"></a>Criar uma nova versão do schema

1. Atualize o esquema na caixa de texto e **selecione Validate**. No exemplo seguinte, foi adicionado um novo campo `id` ao esquema. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Página do Registo de Schema":::    
    
1. Reveja o estado de validação e as alterações e **selecione Guardar**. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Página do Registo de Schema":::     
1. Veja que `2` está selecionado para a **versão** na página **Desaveir do Esquema.** 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Página do Registo de Schema":::    
1. Selecione `1` para ver a versão 1 fo o esquema. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Página do Registo de Schema":::    


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o registo de esquemas, consulte [o Registo Azure Schema em Event Hubs](schema-registry-overview.md).

