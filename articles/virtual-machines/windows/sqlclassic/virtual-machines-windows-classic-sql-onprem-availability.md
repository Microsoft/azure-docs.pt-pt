---
title: Estender grupos de disponibilidade de Always On locais para o Azure | Microsoft Docs
description: Este tutorial usa recursos criados com o modelo de implantação clássico e descreve como usar o assistente para adicionar réplica no SQL Server Management Studio (SSMS) para adicionar uma réplica de grupo de disponibilidade Always On no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978054"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Estender grupos de disponibilidade de Always On locais para o Azure
Always On grupos de disponibilidade fornecem alta disponibilidade para grupos de banco de dados adicionando réplicas secundárias. Essas réplicas permitem o failover de bancos de dados em caso de falha. Além disso, eles podem ser usados para descarregar cargas de trabalho de leitura ou tarefas de backup.

Você pode estender grupos de disponibilidade locais para Microsoft Azure Provisionando uma ou mais VMs do Azure com SQL Server e, em seguida, adicionando-as como réplicas aos seus grupos de disponibilidade locais.

Este tutorial pressupõe que você tenha o seguinte:

* Uma subscrição ativa do Azure. Você pode [se inscrever para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um grupo de disponibilidade Always On existente no local. Para obter mais informações sobre grupos de disponibilidade, consulte [Always on grupos de disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx).
* Conectividade entre a rede local e a rede virtual do Azure. Para obter mais informações sobre como criar essa rede virtual, consulte [criar uma conexão site a site usando o portal do Azure (clássico)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## <a name="add-azure-replica-wizard"></a>Assistente para adicionar réplica do Azure
Esta seção mostra como usar o assistente para **Adicionar réplica do Azure** para estender sua solução de grupo de disponibilidade Always on para incluir réplicas do Azure.

> [!IMPORTANT]
> O **Assistente para adicionar réplica do Azure** só dá suporte a máquinas virtuais criadas com o modelo de implantação clássico. Novas implantações de VM devem usar o modelo do Resource Manager mais recente. Se você estiver usando VMs com o Resource Manager, deverá adicionar manualmente a réplica secundária do Azure usando comandos Transact-SQL (não mostrados aqui). Este assistente não funcionará no cenário do Resource Manager.

1. Em SQL Server Management Studio, expanda **Always on alta disponibilidade** > **grupos de disponibilidade** >  **[nome do seu grupo de disponibilidade]** .
2. Clique com o botão direito do mouse em **réplicas de disponibilidade**e clique em **Adicionar réplica**.
3. Por padrão, o **Assistente para adicionar réplica ao grupo de disponibilidade** é exibido. Clique em **Seguinte**.  Se você tiver selecionado a opção não **Mostrar esta página novamente** na parte inferior da página durante uma inicialização anterior deste assistente, essa tela não será exibida.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Você será solicitado a se conectar a todas as réplicas secundárias existentes. Você pode clicar em **conectar...** ao lado de cada réplica ou você pode clicar em **conectar tudo...** na parte inferior da tela. Após a autenticação, clique em **Avançar** para avançar para a próxima tela.
5. Na página **especificar réplicas** , várias guias são listadas na parte superior: **réplicas**, **pontos de extremidade**, **preferências de backup**e **ouvinte**. Na guia **réplicas** , clique em **Adicionar réplica do Azure...** para iniciar o assistente para adicionar réplica do Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Selecione um certificado de gerenciamento do Azure existente no repositório de certificados do Windows local se você tiver instalado um antes. Selecione ou insira a ID de uma assinatura do Azure, se você tiver usado uma antes. Você pode clicar em baixar para baixar e instalar um certificado de gerenciamento do Azure e baixar a lista de assinaturas usando uma conta do Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Você preencherá cada campo na página com valores que serão usados para criar a VM (máquina virtual) do Azure que hospedará a réplica.
   
   | Definição | Descrição |
   | --- | --- |
   | **Imagem** |Selecione a combinação desejada de sistema operacional e SQL Server |
   | **Tamanho da VM** |Selecione o tamanho da VM que melhor atenda às suas necessidades de negócios |
   | **Nome da VM** |Especifique um nome exclusivo para a nova VM. O nome deve conter entre 3 e 15 caracteres, pode conter apenas letras, números e hifens e deve começar com uma letra e terminar com uma letra ou número. |
   | **Nome de usuário da VM** |Especifique um nome de usuário que se tornará a conta de administrador na VM |
   | **Senha de administrador da VM** |Especifique uma senha para a nova conta |
   | **Confirmar senha** |Confirme a senha da nova conta |
   | **Rede Virtual** |Especifique a rede virtual do Azure que a nova VM deve usar. Para obter mais informações sobre redes virtuais, consulte [visão geral da rede virtual](../../../virtual-network/virtual-networks-overview.md). |
   | **Sub-rede da rede virtual** |Especifique a sub-rede da rede virtual que a nova VM deve usar |
   | **Domínio** |Confirme se o valor preenchido previamente para o domínio está correto |
   | **Nome de usuário de domínio** |Especifique uma conta que esteja no grupo local de administradores nos nós do cluster local |
   | **Palavra-passe** |Especifique a senha para o nome de usuário de domínio |
8. Clique em **OK** para validar as configurações de implantação.
9. Os termos legais são exibidos em seguida. Leia e clique em **OK** se você concordar com estes termos.
10. A página **especificar réplicas** é exibida novamente. Verifique as configurações da nova réplica do Azure nas guias **réplicas**, **pontos de extremidade**e **preferências de backup** . Modifique as configurações para atender às suas necessidades de negócios.  Para obter mais informações sobre os parâmetros contidos nessas guias, consulte a [página especificar réplicas (Assistente de novo grupo de disponibilidade/Wizard de adição de réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Observe que os ouvintes não podem ser criados usando a guia ouvinte para grupos de disponibilidade que contêm réplicas do Azure. Além disso, se um ouvinte já tiver sido criado antes de iniciar o assistente, você receberá uma mensagem indicando que não há suporte para ele no Azure. Veremos como criar ouvintes na seção **criar um ouvinte de grupo de disponibilidade** .
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Clique em **Seguinte**.
12. Selecione o método de sincronização de dados que você deseja usar na página **selecionar sincronização de dados inicial** e clique em **Avançar**. Para a maioria dos cenários, selecione **sincronização de dados completa**. Para obter mais informações sobre métodos de sincronização de dados, consulte a [página Selecionar sincronização de dados inicial (assistentes Always on grupo de disponibilidade)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Examine os resultados na página **validação** . Corrija os problemas pendentes e execute novamente a validação, se necessário. Clique em **Seguinte**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Examine as configurações na página **Resumo** e clique em **concluir**.
15. O processo de provisionamento é iniciado. Quando o assistente for concluído com êxito, clique em **fechar** para sair do assistente.

> [!NOTE]
> O assistente para adicionar réplica do Azure cria um arquivo de log no Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Esse arquivo de log pode ser usado para solucionar problemas de implantações de réplica do Azure com falha. Se o assistente falhar ao executar qualquer ação, todas as operações anteriores serão revertidas, incluindo a exclusão da VM provisionada.
> 
> 

## <a name="create-an-availability-group-listener"></a>Criar um ouvinte de grupo de disponibilidade
Depois que o grupo de disponibilidade tiver sido criado, você deverá criar um ouvinte para os clientes se conectarem às réplicas. Os ouvintes direcionam conexões de entrada para a réplica primária ou secundária somente leitura. Para obter mais informações sobre ouvintes, consulte [configurar um ouvinte de ILB para grupos de disponibilidade Always on no Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Passos seguintes
Além de usar o **Assistente para adicionar réplica do Azure** para estender seu grupo de disponibilidade Always on para o Azure, você também pode mover algumas cargas de trabalho de SQL Server completamente para o Azure. Para começar, consulte [Provisionando uma máquina Virtual SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados à execução de SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

