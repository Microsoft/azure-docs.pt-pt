---
title: Mudança no lugar da versão SQL Server
description: Saiba como alterar a versão da sua máquina virtual SQL Server em Azure.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 70085cda4252e5435c450ae80dae2e49829b1474
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101812"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Mudança no local da versão do servidor SQL em Azure VM

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como alterar a versão do Microsoft SQL Server numa máquina virtual Windows (VM) no Microsoft Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma atualização no local do SQL Server, aplicam-se as seguintes condições:

- É necessário o meio de configuração da versão desejada do SQL Server. Os clientes que tenham [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter os seus meios de instalação a partir do [Centro de Licenciamento de Volume.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Os clientes que não têm Software Assurance podem utilizar os meios de configuração a partir de uma imagem VM do Azure Marketplace SQL Server que tem uma versão posterior do SQL Server (normalmente localizado em C:\SQLServerFull).
- As atualizações de edição devem seguir os [caminhos de upgrade](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15)de suporte .

## <a name="planning-for-version-change"></a>Planeamento para mudança de versão

Recomendamos que reveja os seguintes itens antes de alterar a versão:

1. Confira as novidades na versão que está a planear atualizar para:

   - Novidades no [SQL 2019](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-ver15?view=sql-server-ver15)
   - Novidades no [SQL 2017](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2017?view=sql-server-ver15)
   - Novidades no [SQL 2016](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-ver15)
   - Novidades no [SQL 2014](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-2014)

1. Recomendamos que verifique a certificação de [compatibilidade](https://docs.microsoft.com/sql/database-engine/install-windows/compatibility-certification?view=sql-server-ver15) para a versão a que vai alterar para que possa utilizar os modos de compatibilidade da base de dados para minimizar o efeito da atualização.
1. Pode rever os seguintes artigos para ajudar a garantir um resultado bem sucedido:

   - [Vídeo: Modernizar o SQL Server Pam Lahoud & Pedro Lopes 20 Anos de PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Assistente de experimentação de base de dados para testes de AB](https://docs.microsoft.com/sql/dea/database-experimentation-assistant-overview?view=sql-server-ver15)
   - [Atualizar bases de dados utilizando o Assistente de Afinação de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/upgrade-dbcompat-using-qta?view=sql-server-ver15)
   - [Alterar o Nível de Compatibilidade da Base de Dados e utilizar a Loja de Consultas](https://docs.microsoft.com/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store?view=sql-server-ver15)

## <a name="upgrade-sql-version"></a>Atualizar versão SQL

> [!WARNING]
> A atualização da versão do SQL Server reiniciará o serviço para o SQL Server, além de quaisquer serviços associados, tais como Serviços de Análise e Serviços R.

Para atualizar a versão do SQL Server, obtenha o suporte de configuração do SQL Server para a versão posterior que [suportasse o caminho](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15) de upgrade do SQL Server e faça os seguintes passos:

1. Faça o back up das bases de dados, incluindo o sistema (exceto a tempdb) e as bases de dados dos utilizadores, antes de iniciar o processo. Também pode criar uma cópia de segurança de nível VM consistente com aplicações utilizando os Serviços de Backup Azure.
1. Comece Setup.exe a partir dos meios de instalação do SQL Server.
1. O Assistente de Instalação inicia o Centro de Instalação do Servidor SQL. Para atualizar uma instância existente do SQL Server, selecione **Instalação** no painel de navegação e, em seguida, **selecione Upgrade a partir de uma versão anterior do SQL Server**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Seleção para atualização da versão do SQL Server":::

1. Na página **'Chave do Produto',** selecione uma opção para indicar se está a atualizar para uma edição gratuita do SQL Server ou se tem uma chave PID para uma versão de produção do produto. Para mais informações, consulte [Edições e funcionalidades suportadas do SQL Server 2019 (15.x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15) e [das atualizações de versão e edição suportadas (SQL Server 2016)](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades?view=sql-server-ver15).
1. Selecione **Next** até chegar à página **De atualização pronta** e, em seguida, selecione **Upgrade**. A janela de configuração pode parar de responder durante alguns minutos enquanto a mudança está a fazer efeito. Uma página **Completa** confirmará que a sua atualização está concluída. Para um procedimento passo a passo para atualizar, consulte [o procedimento completo](https://docs.microsoft.com/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup?view=sql-server-ver15#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Página completa":::

Se alterou a edição do SQL Server para além de alterar a versão, também atualize a edição e consulte a **Versão e edição verificar na** secção Portal para alterar a instância VM SQL.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Alterar metadados de versão":::

## <a name="downgrade-the-version-of-sql-server"></a>Downgrade a versão do SQL Server

Para desclassificar a versão do SQL Server, tem de desinstalar completamente o SQL Server e reinstalá-lo novamente utilizando a versão desejada. Isto é semelhante a uma nova instalação do SQL Server porque não será capaz de restaurar a base de dados anterior de uma versão posterior para a versão anterior recentemente instalada. As bases de dados terão de ser recriadas do zero. Se também alterou a edição do SQL Server durante a atualização, altere a propriedade **Edition** do SQL Server VM no portal Azure para o valor da nova edição. Isto atualiza os metadados e faturação que estão associados a este VM.

> [!WARNING]
> Não é suportada uma degradação no local do SQL Server.

Pode desclassificar a versão do SQL Server seguindo estes passos:

1. Certifique-se de que não está a utilizar qualquer funcionalidade que esteja [disponível apenas na versão posterior.](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx)
1. Resquipe todas as bases de dados, incluindo o sistema (exceto a tempdb) e as bases de dados dos utilizadores.
1. Exporte todos os objetos ao nível do servidor necessários (tais como gatilhos de servidores, funções, logins, servidores ligados, empregos, credenciais e certificados).
1. Se não tiver scripts para recriar as bases de dados dos seus utilizadores na versão anterior, tem de enviar todos os objetos e exportar todos os dados utilizando BCP.exe, SSIS ou DACPAC.

   Certifique-se de que seleciona as opções corretas quando scripts itens como a versão alvo, objetos dependentes e opções avançadas.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Opções de script":::

1. Desinstale completamente o SQL Server e todos os serviços associados.
1. Reinicie a VM.
1. Instale o SQL Server utilizando os meios de comunicação para a versão desejada do programa.
1. Instale os mais recentes pacotes de serviços e atualizações cumulativas.
1. Importar todos os objetos ao nível do servidor necessários (que foram exportados no passo 3).
1. Re-crie todas as bases de dados necessárias do utilizador do zero (utilizando scripts criados ou os ficheiros do Passo 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>Verifique a versão e edição no portal

Depois de alterar a versão do SQL Server, registe novamente o seu SQL Server VM com o [fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md) para que possa utilizar o portal Azure para visualizar a versão do SQL Server. O número da versão listada deve agora refletir a versão e edição recentemente atualizadas da sua instalação SQL Server.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Verificar versão":::

> [!NOTE]
> Se já tiver o Fornecedor de Recursos instalado, [não registe o Recurso SQL VM](sql-vm-resource-provider-register.md#unregister-from-the-resource-provider) e, em seguida, [registe novamente o recurso SQL VM](sql-vm-resource-provider-register.md#register-a-subscription-with-the-resource-provider) para que detete a versão e edição corretas do SQL Server que está instalado no VM. Isto atualiza os metadados e informações de faturação que estão associados a este VM.

## <a name="remarks"></a>Observações

- Recomendamos que inicie backups/atualizar estatísticas/reconstruir índices/verificar a consistência após o final da atualização. Também pode verificar os níveis de compatibilidade da base de dados individuais para se certificar de que refletem o nível desejado.
- Depois de o SQL Server ser atualizado no VM, certifique-se de que a propriedade **Edition** do SQL Server no portal Azure corresponde ao número de edição instalado para faturação.
- A capacidade de [alterar a edição](change-sql-server-edition.md#change-edition-in-portal) é uma característica do fornecedor de recursos SQL VM. A implementação de uma imagem do Azure Marketplace através do portal Azure regista automaticamente um SQL Server VM com o fornecedor de recursos. No entanto, os clientes que se instalam automaticamente no SQL Server terão de registar manualmente [o seu SQL Server VM](sql-vm-resource-provider-register.md).
- Se deixar cair o seu recurso VM do S SQL Server, a definição de edição codificada dura da imagem é restaurada.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos:

- [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
- [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
- [Notas de lançamento para SQL Server num VM do Windows](doc-changes-updates-release-notes.md)
