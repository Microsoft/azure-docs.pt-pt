---
title: Descubra as instâncias do SQL Server num projeto existente da Azure Migrate
description: Saiba como descobrir as instâncias do SQL Server num projeto existente da Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567157"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Descubra as instâncias do SQL Server num projeto existente 

Este artigo descreve como descobrir instâncias e bases de dados do SQL Server num projeto [Azure Migrate](./migrate-services-overview.md) que foi criado antes da pré-visualização da funcionalidade de avaliação do Azure SQL.

Descobrir instâncias e bases de dados do SQL Server em funcionamento em máquinas no local ajuda a identificar e adaptar um caminho de migração para Azure SQL. O aparelho Azure Migrate executa esta descoberta utilizando as credenciais de autenticação do Domínio ou as credenciais de autenticação do SQL Server que têm acesso às instâncias e bases de dados do SQL Server em execução nos servidores direcionados. Este processo de descoberta é sem agente, ou seja, nada está instalado nos servidores alvo.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de que:
    - Criei um [projeto Azure Migrate](./create-manage-projects.md) antes do anúncio do recurso de avaliação SQL para a sua região
    - Acrescentou o [Azure Migrate: Descoberta e](./how-to-assess.md) ferramenta de avaliação a um projeto
- Reveja [o suporte e requisitos de descoberta de aplicações.](./migrate-support-matrix-vmware.md#vmware-requirements)
-  Certifique-se de que os servidores onde está a executar a descoberta de aplicações têm a versão PowerShell 2.0 ou posteriormente instalada, e as Ferramentas VMware (mais tarde de 10.2.0) estão instaladas.
- Verifique os [requisitos](./migrate-appliance.md) para a colocação do aparelho Azure Migrate.
- Verifique se tem as [funções necessárias](./create-manage-projects.md#verify-permissions) na subscrição para criar recursos.
- Certifique-se de que o seu aparelho tem acesso à internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Permitir a descoberta de instâncias e bases de dados do SQL Server

1. No seu Projeto Azure Migrate, também
    - Selecione **Não ativado** no azulejo hub, ou   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="azulejo hub Azure Migrate com descoberta SQL não ativada":::
    - Selecione **Não ativado** em qualquer entrada na página de descoberta do Servidor sob a coluna SQL instances   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure Migrate descobriu a lâmina de servidores com descoberta SQL não ativada":::
2. No Discover SQL Server as instâncias e bases de dados seguem os passos implicados:
    - Selecione **Upgrade**, para criar o recurso necessário.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Botão para atualizar o aparelho Azure Migrate":::
    - Valide que os serviços em funcionamento no aparelho são atualizados para as versões mais recentes. Para tal, lance o gestor de configuração do aparelho a partir do servidor do seu aparelho e selecione os serviços de visualização do aparelho a partir do painel de pré-requisitos de configuração.
        - Aparelho e seus componentes são automaticamente atualizados :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Verifique a versão do aparelho":::
    - No painel de credenciais e fontes de descoberta do gestor de configuração do aparelho, adicione credenciais de autenticação de servidor de domínio ou SQL que tenham acesso Sysadmin na instância e bases de dados do SQL Server a serem descobertas.
    Pode aproveitar a função automática de mapeamento de credenciais do aparelho ou mapear manualmente as credenciais para o respetivo servidor, conforme [aqui](./tutorial-discover-vmware.md#start-continuous-discovery)destacado .

    Alguns pontos a notar:
    - Certifique-se de que o inventário de software já está ativado ou forneça credenciais de domínio ou não domínio para ativar o mesmo. O inventário de software deve ser realizado para descobrir as instâncias do SQL Server.
    - O aparelho tentará validar as credenciais de domínio com AD, tal como são adicionadas. Certifique-se de que o servidor do aparelho tem linha de visão de rede para o servidor AD associado às credenciais. As credenciais associadas à autenticação do servidor SQL não são validadas.

3. Uma vez adicionadas as credenciais desejadas, selecione Iniciar a Descoberta, para iniciar a verificação.

> [!Note]
>Por favor, permita que a descoberta SQL corra por algum tempo antes de criar avaliações para Azure SQL. Se a descoberta de instâncias e bases de dados do SQL Server não for concluída, as respetivas instâncias são marcadas como **Desconhecidas** no relatório de avaliação.

## <a name="next-steps"></a>Passos seguintes

- Saiba como criar uma [avaliação Azure SQL](./how-to-create-azure-sql-assessment.md)
- Saiba mais sobre [avaliações do Azure SQL](./concepts-azure-sql-assessment-calculation.md)