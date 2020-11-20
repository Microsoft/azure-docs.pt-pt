---
title: Implementar monitor de Azure para soluções SAP com o portal Azure
description: Implementar monitor de Azure para soluções SAP com o portal Azure
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 08/17/2020
ms.reviewer: cynthn
ms.openlocfilehash: c3b3848e4e4f7b0445f882265dbe66bb10b48833
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968592"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Implementar monitor de Azure para soluções SAP com portal Azure

Os recursos do Azure Monitor for SAP Solutions podem ser criados através do [portal Azure.](https://azure.microsoft.com/features/azure-portal) Este método fornece uma interface de utilizador baseada no navegador para implementar o Azure Monitor para soluções SAP e configurar fornecedores.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no portal do Azure em https://portal.azure.com

## <a name="create-monitoring-resource"></a>Criar recurso de monitorização

1. Selecione **Azure Monitor para soluções SAP** do **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="A imagem mostra a seleção do Azure Monitor para soluções SAP da Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. No separador **Básicos,** forneça os valores necessários. Se aplicável, pode utilizar um espaço de trabalho log analytics existente.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Exibição das opções de configuração do portal Azure." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Ao selecionar uma rede virtual, certifique-se de que os sistemas que pretende monitorizar são alcançáveis a partir desse VNET. 

   > [!IMPORTANT]
   > A seleção **do Share** for Data sharing com a Microsoft permite que as nossas equipas de suporte forneçam suporte adicional.

## <a name="configure-providers"></a>Configure fornecedores

### <a name="sap-hana-provider"></a>Fornecedor SAP HANA 

1. Selecione o **separador 'Fornecedor'** para adicionar os fornecedores que pretende configurar. Pode adicionar vários fornecedores um após o outro ou adicioná-los depois de implementar o recurso de monitorização. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Mostra o separador fornecedor para adicionar fornecedores adicionais ao seu Azure Monitor para soluções SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. **Selecione Adicionar o fornecedor** e escolha SAP **HANA** a partir da queda para baixo. 

   > [!IMPORTANT]
   > Certifique-se de que o fornecedor SAP HANA está configurado para o nó sap HANA 'master'.

3. Insira o IP privado para o servidor HANA.

4. Insira o nome do inquilino da Base de Dados que pretende utilizar. No entanto, pode escolher qualquer inquilino, recomendamos a utilização **do SYSTEMDB,** pois permite um leque mais alargado de áreas de monitorização. 

5. Insira o número da porta SQL associado à sua base de dados HANA. O número da porta deve estar no formato de **[3]**  +  **[instância#]**  +  **[13]**. Por exemplo, 30013. 

6. Insira o nome de utilizador da Base de Dados que pretende utilizar. Certifique-se de que o utilizador da base de dados tem as funções **de monitorização** e leitura do **catálogo** atribuídas. 

7. Quando terminar, **selecione Adicionar fornecedor**. Continue a adicionar fornecedores adicionais conforme necessário ou selecione **Review + criar** para completar a implementação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Imagem das opções de configuração ao adicionar informações do fornecedor." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Fornecedor de cluster de alta disponibilidade (Pacemaker)

1. Selecione **cluster de alta disponibilidade (Pacemaker)** a partir da queda para baixo. 

   > [!IMPORTANT]
   > Para configurar o fornecedor de cluster de alta disponibilidade (Pacemaker), certifique-se de que ha_cluster_provider é instalada em cada nó. Para mais informações consulte [o exportador de clusters HA](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Insira o ponto final prometheus na forma de http://IP:9664/metrics . 
 
3. Insira o ID do sistema (SID), nome de hospedeiro e nome do cluster.

4. Quando terminar, **selecione Adicionar fornecedor**. Continue a adicionar fornecedores adicionais conforme necessário ou selecione **Review + criar** para completar a implementação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="A imagem mostra opções relacionadas com o fornecedor de pacemaker de cluster HA." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="microsoft-sql-server-provider"></a>Fornecedor de servidores Microsoft SQL

1. Antes de adicionar o fornecedor microsoft SQL Server, deverá executar o seguinte script no SQL Server Management Studio para criar um utilizador com as permissões necessárias para configurar o fornecedor.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. **Selecione Adicionar o fornecedor** e escolha o Microsoft **SQL Server** a partir da queda. 

3. Preencha os campos utilizando informações associadas ao seu Microsoft SQL Server. 

4. Quando terminar, **selecione Adicionar fornecedor**. Continue a adicionar fornecedores adicionais conforme necessário ou selecione **Review + criar** para completar a implementação.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="A imagem mostra informações relacionadas com a adição do Fornecedor de Servidores SQL da Microsoft." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Monitor para Soluções SAP](azure-monitor-overview.md)
