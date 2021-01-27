---
title: Registar e digitalizar um inquilino power BI (pré-visualização)
description: Saiba como usar o portal Azure Purview para registar e digitalizar um inquilino power BI.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 5f42b5f2b2a7660ee0de975068f64572c470503a
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874911"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registar e digitalizar um inquilino power BI (pré-visualização)

Este artigo mostra como usar o portal Azure Purview para registar e digitalizar um inquilino do Power BI.

> [!Note]
> Se a instância de Purview e o inquilino do Power BI estiverem no mesmo inquilino Azure, só pode utilizar a autenticação de identidade gerida (MSI) para configurar uma digitalização de um inquilino do Power BI. 

## <a name="create-a-security-group-for-permissions"></a>Criar um grupo de segurança para permissões

Para configurar a autenticação, crie um grupo de segurança e adicione-lhe a identidade gerida do catálogo.

1. No [portal Azure,](https://portal.azure.com)procure **o Azure Ative Directory**.
1. Crie um novo grupo de segurança no seu Azure Ative Directory, seguindo [criar um grupo básico e adicionar membros usando o Azure Ative Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Pode saltar este passo se já tiver um grupo de segurança que pretende usar.

1. Selecione **a Segurança** como **o Tipo de Grupo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo de grupo de segurança":::

1. Adicione a identidade gerida do seu catálogo a este grupo de segurança. Selecione **Membros,** em seguida, **selecione + Adicione os membros**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adicione a instância gerida do catálogo ao grupo.":::

1. Procure o seu catálogo e selecione-o.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Adicione catálogo procurando-o":::

    Deve ver uma notificação de sucesso mostrando que foi adicionada.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Adicione o sucesso do CATÁLOGO MSI":::

## <a name="associate-the-security-group-with-the-tenant"></a>Associe o grupo de segurança ao inquilino

1. Inicie sessão no [portal de administração Power BI](https://app.powerbi.com/admin-portal/tenantSettings).
1. Selecione a página **de definições do Inquilino.**

    > [!Important]
    > Você precisa ser um Power BI Admin para ver a página de configurações do inquilino.

1. Selecione **as definições de API Admin**  >  **Permite que os principais de serviço utilizem APIs de administração de energia apenas de leitura (Pré-visualização)**.
1. Selecione **grupos de segurança específicos.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Imagem mostrando como permitir que os diretores de serviço obtenham permissões de API de administração de power bi apenas de leitura":::

    > [!Caution]
    > Quando permite que o grupo de segurança que criou (que tem o seu catálogo de dados gerido identidade como membro) utilize apis de administração de power bi apenas de leitura, também lhe permite aceder aos metadados (por exemplo, dashboard e reportar nomes, proprietários, descrições, etc.) para todos os seus artefactos Power BI neste inquilino. Uma vez que os metadados foram puxados para o Azure Purview, as permissões do Purview, não permissões power BI, determinam quem pode ver esses metadados.

    > [!Note]
    > Pode remover o grupo de segurança das definições do seu programador, mas os metadados anteriormente extraídos não serão removidos da conta ' '' '' Pode eliminá-lo separadamente, se desejar.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Registe o seu Power BI e crie uma varredura

Agora que deu permissões ao catálogo para se ligar à ADMIN API do seu inquilino Power BI, pode configurar a sua digitalização a partir do portal do catálogo.

Primeiro, adicione uma bandeira de recurso especial ao seu URL de Purview 

1. Selecione o ícone **do Centro de Gestão.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Ícone do centro de gestão.":::

1. Em seguida, selecione **+ Novas** **fontes de dados**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Imagem de novo botão de fonte de dados":::

    Selecione **Power BI** como fonte de dados.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Imagem mostrando a lista de fontes de dados disponíveis para escolher":::

1. Dê ao seu exemplo de Power BI um nome amigável.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Imagem mostrando nome amigo da fonte de dados do Power BI":::

    O nome deve ter entre 3 a 63 caracteres e deve conter apenas letras, números, sublinhados e hífenes.  Espaços não são permitidos.

    Por padrão, o sistema irá encontrar o inquilino power BI que existe na mesma subscrição Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Fonte de dados do Power BI registada":::

1. Dê um nome à sua tomografia. Note que o único método de autenticação suportado é **identidade gerida.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Imagem mostrando configuração de digitalização de BI de energia":::

    O nome da varredura deve ter entre 3-63 caracteres e deve conter apenas letras, números, sublinhados e hífenes.  Espaços não são permitidos.

1. Instale um gatilho de digitalização. As suas opções são **Uma Vez,** **A cada 7 dias,** e **a cada 30 dias.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Digitalizar a imagem do gatilho":::

1. Na **nova verificação,** selecione **Save and Run** para lançar a sua digitalização.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Guardar e executar a imagem do ecrã do Power BI":::

## <a name="next-steps"></a>Próximos passos

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)