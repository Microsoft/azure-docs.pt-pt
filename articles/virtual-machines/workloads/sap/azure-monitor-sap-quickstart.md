---
title: Implementar monitor de Azure para soluções SAP com o portal Azure
description: Implementar monitor de Azure para soluções SAP com o portal Azure
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 908de54ee66772f3eb648895529c843675c3bf15
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538636"
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

### <a name="sap-netweaver-provider"></a>Fornecedor SAP NetWeaver

#### <a name="prerequisites-for-adding-netweaver-provider"></a>Pré-requisitos para adicionar fornecedor NetWeaver

O "serviço de arranque SAP" fornece uma série de serviços, incluindo a monitorização do sistema SAP. Estamos a aproveitar o "SAPControl" que é uma interface de serviço web SOAP que expõe estas capacidades. Esta interface de serviço web SAPControl diferencia entre métodos de webservice [protegidos e desprotegidos.](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) Para conseguir obter métricas específicas, terá de desprotegir alguns métodos. Para desprotegir os métodos necessários para a libertação atual, siga os passos abaixo para cada sistema SAP:

1. Abra uma ligação SAP GUI ao servidor SAP
2. Faça login usando uma conta administrativa
3. Executar transação RZ10
4. Selecione o perfil apropriado (DEFAULT. PFL)
5. Selecione 'Manutenção Estendida' e clique em Alterar 
6. Modifique o valor do parâmetro afetado "service/protectedwebmethods" para "SDEFAULT -GetQueueStatistic – ABAPGetWPTable – EnqGetStatistic –GetProcessList" para a definição recomendada e clique em Copy
7. Volte e selecione Profile->Save
8. Reiniciar o sistema para que o parâmetro entre em vigor

>[!Tip]
> Utilize uma Lista de Controlo de Acesso (ACL) para filtrar o acesso a uma porta de servidor. Consulte a sua [nota SAP](https://launchpad.support.sap.com/#/notes/1495075)

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Instalação do fornecedor NetWeaver no portal Azure
1.  Certifique-se de que os passos pré-requisitos foram concluídos e o servidor foi reiniciado
2.  No portal Azure, em AMS, selecione Adicionar fornecedor e escolha SAP NetWeaver a partir da queda para baixo
3.  Insira o nome de anfitrião do sistema SAP e subdomínio (se aplicável)
4.  Introduza o número de instância correspondente ao nome de anfitrião introduzido 
5.  Introduza o ID do sistema (SID)
6.  Quando terminar, selecione Adicionar fornecedor
7.  Continue a adicionar fornecedores adicionais conforme necessário ou selecione Review + criar para completar a implementação

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

### <a name="sap-hana-provider"></a>Fornecedor SAP HANA 

1. Selecione o **separador 'Fornecedor'** para adicionar os fornecedores que pretende configurar. Pode adicionar vários fornecedores um após o outro ou adicioná-los depois de implementar o recurso de monitorização. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Mostra o separador fornecedor para adicionar fornecedores adicionais ao seu Azure Monitor para soluções SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. **Selecione Adicionar o fornecedor** e escolha SAP **HANA** a partir do drop-down. 

   > [!IMPORTANT]
   > Certifique-se de que o fornecedor SAP HANA está configurado para o nó sap HANA 'master'.

3. Insira o IP privado para o servidor HANA.

4. Insira o nome do inquilino da Base de Dados que pretende utilizar. No entanto, pode escolher qualquer inquilino, recomendamos a utilização **do SYSTEMDB,** pois permite um leque mais alargado de áreas de monitorização. 

5. Insira o número da porta SQL associado à sua base de dados HANA. O número da porta deve estar no formato de **[3]**  +  **[instância#]**  +  **[13]**. Por exemplo, 30013. 

6. Insira o nome de utilizador da Base de Dados que pretende utilizar. Certifique-se de que o utilizador da base de dados tem as funções **de monitorização** e leitura do **catálogo** atribuídas. 

7. Quando terminar, **selecione Adicionar fornecedor**. Continue a adicionar mais fornecedores conforme necessário ou selecione **Review + criar** para completar a implementação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Imagem das opções de configuração ao adicionar informações do fornecedor." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::
   
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

2. **Selecione Adicionar o fornecedor** e escolha o Microsoft **SQL Server** a partir do drop-down. 

3. Preencha os campos utilizando informações associadas ao seu Microsoft SQL Server. 

4. Quando terminar, **selecione Adicionar fornecedor**. Continue a adicionar mais fornecedores conforme necessário ou selecione **Review + criar** para completar a implementação.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="A imagem mostra informações relacionadas com a adição do Fornecedor de Servidores SQL da Microsoft." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Fornecedor de cluster de alta disponibilidade (Pacemaker)

1. Selecione **cluster de alta disponibilidade (Pacemaker)** a partir do drop-down. 

   > [!IMPORTANT]
   > Para configurar o fornecedor de cluster de alta disponibilidade (Pacemaker), certifique-se de que ha_cluster_provider é instalada em cada nó. Para mais informações consulte [o exportador de clusters HA](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Insira o ponto final prometheus na forma de http://IP:9664/metrics . 
 
3. Insira o ID do sistema (SID), nome de hospedeiro e nome do cluster.

4. Quando terminar, **selecione Adicionar fornecedor**. Continue a adicionar mais fornecedores conforme necessário ou selecione **Review + criar** para completar a implementação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="A imagem mostra opções relacionadas com o fornecedor de pacemaker de cluster HA." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::

### <a name="os-linux-provider"></a>Fornecedor de OS (Linux) 

1. Selecione OS (Linux) a partir do drop-down 

   >[!IMPORTANT]
   > Para configurar o fornecedor OS (Linux), certifique-se de que a versão mais recente do Node_Exporter está instalada em cada hospedeiro (BareMetal ou VM) que pretende monitorizar. Use este [link](https://prometheus.io/download/#node_exporter) para encontrar a versão mais recente. Para mais informações, consulte [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Insira um nome, que será o identificador para a Instância BareMetal.
3. Insira o ponto final do exportador de nó sob a forma de http://IP:9100/metrics .

   >[!IMPORTANT]
   >Por favor, use o endereço IP privado do anfitrião linux. Certifique-se de que o hospedeiro e o recurso AMS estão no mesmo VNET. 

   >[!Note]
   > A porta de firewall "9100" deve ser aberta no hospedeiro linux.
   >Se utilizar firewall-cmd: firewall-cmd --permanente --add-port=9100/tcp firewall-cmd --recarregar Se utilizar ufw: ufw permitir recarga ufw 9100/tcp

    >[!Tip]
    > Se o anfitrião linux for um VM Azure, certifique-se de que todos os NSGs aplicáveis permitem o tráfego de entrada na porta 9100 a partir de "VirtualNetwork" como fonte.
 
5. Quando terminar, **selecione Adicionar fornecedor**. Continue a adicionar mais fornecedores conforme necessário ou selecione **Review + criar** para completar a   implementação. 


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Monitor para Soluções SAP](azure-monitor-overview.md)
