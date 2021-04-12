---
title: Como migrar - portal
description: Como migrar para os Serviços cloud (suporte alargado) usando o portal Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286955"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Migrar para serviços em nuvem (suporte alargado) usando o portal Azure

Este artigo mostra-lhe como usar o portal Azure para migrar dos Serviços cloud [(clássico)](../cloud-services/cloud-services-choose-me.md) para os [Serviços cloud (suporte alargado)](overview.md).

> [!IMPORTANT]
> A migração dos Serviços cloud (clássico) para os Serviços cloud (suporte alargado) utilizando a ferramenta de migração está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Antes de começar

**Certifique-se de que é administrador da subscrição.**

Para realizar esta migração, tem de ser adicionado como coadministrator para a subscrição no [portal Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu **Hub,** **selecione Subscrição**. Se não o vir, selecione **Todos os serviços**.
3. Encontre a entrada de subscrição adequada e, em seguida, olhe para o campo **MY ROLE.** Para um coadministrador, o valor deve ser *a administração da conta.*

Se não conseguir adicionar um coadministrador, contacte um administrador de serviço ou [coadministrador](../role-based-access-control/classic-administrators.md) para que a subscrição seja adicionada.

**Inscreva-se no fornecedor de recursos migratórios**

1. Registe-se com o fornecedor de recursos de migração `Microsoft.ClassicInfrastructureMigrate` e funcionalidade de pré-visualização `Cloud Services` no espaço de nomes microsoft.Compute utilizando o [portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1).  
1. Aguarde cinco minutos para que a inscrição esteja concluída e verifique o estado da aprovação. 

## <a name="migrate-your-cloud-service-resources"></a>Migrar os seus recursos de Cloud Service

1. Vá à [lâmina do portal Cloud Services (clássico).](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames) 
2. Selecione o Serviço de Cloud que pretende migrar.
3. Selecione a **lâmina Migração para ARM.**

    > [!NOTE]
    > Se migrar um Cloud Services (clássico) localizado dentro de uma rede virtual (clássica), aparecerá uma mensagem de banner que o levará a mover a lâmina de rede virtual (clássica).
    > Você será levado para a lâmina de rede virtual (clássica) para completar a migração tanto da rede virtual (clássica) como dos serviços cloud (clássicos) dentro dela.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="A imagem mostra mover um clássico da rede virtual no portal Azure.":::
 

4. Validar a migração. 

    Se a validação for bem sucedida, todas as implementações são suportadas e prontas para serem preparadas.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="A imagem mostra a lâmina Migração para ARM no portal Azure.":::

    Se a validação falhar, será apresentada uma lista de cenários não apoiados e terá de ser corrigida antes de a migração poder continuar. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="A imagem mostra erro de validação no portal Azure.":::

5. Preparem-se para a migração.

    Se a preparação for bem sucedida, a migração está pronta para se comprometer.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="A imagem mostra a validação a passar no portal Azure.":::

    Se a preparação falhar, reveja o erro, resolva quaisquer problemas e reveja a preparação. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="A imagem mostra erro de falha de validação.":::

      Depois de Preparar, todos os Serviços Cloud de uma rede virtual estão disponíveis para operações de leitura usando tanto os Serviços Cloud (clássico) como os Serviços Cloud (suporte alargado) Azure portal blade. A implementação do Cloud Service (suporte alargado) pode agora ser testada para garantir o bom funcionamento antes de finalizar a migração. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="A imagem mostra os APIs de teste na lâmina do portal.":::

6.  **(Opcional)** Abortar a migração. 
    
    Se optar por descontinuar a migração, utilize o botão **Abortar** para reverter os passos anteriores. A implementação cloud Services (clássica) é então desbloqueada para todas as operações da CRUD.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="A imagem mostra a validação passando.":::

    Se o aborto falhar, selecione **Retry abortar**. Uma nova mente deve resolver o problema. Caso contrário, contacte o suporte. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="A imagem mostra a mensagem de erro de validação.":::

7.  Cometa migração.

    >[!IMPORTANT]
    > Uma vez que se comprometa com a migração, não há opção para recuar. 
    
    Digite em "sim" para confirmar e comprometer-se com a migração. A migração está agora completa. A implantação de Cloud Services (suporte alargado) em migração está desbloqueada para todas as operações". 

## <a name="next-steps"></a>Passos seguintes
Reveja a secção [de alterações de migração post](in-place-migration-overview.md#post-migration-changes) para ver alterações nos ficheiros de implementação, automação e outros atributos da sua nova implementação de Serviços cloud (suporte alargado). 
