---
title: SQL Server Business Intelligence | Documentos da Microsoft
description: Este tópico utiliza recursos criados com o modelo de implementação clássica e descreve os recursos de Business Intelligence (BI) disponíveis do SQL Server em execução em máquinas virtuais do Azure (VMs).
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 29e851772e665b4130ee58b04c264d55bcd54523
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317787"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence em Máquinas Virtuais do Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

A Galeria de Máquina Virtual do Microsoft Azure inclui imagens que contêm instalações do SQL Server. As edições do SQL Server suportadas nas imagens de galeria são os mesmos arquivos de instalação, que pode instalar computadores no local e as máquinas virtuais. Este tópico resume as funcionalidades do SQL Server Business Intelligence (BI) instaladas nas imagens e passos de configuração necessários após aprovisionar uma máquina virtual. Este tópico também descreve as topologias de implementação suportado para recursos de BI e as melhores práticas.

## <a name="license-considerations"></a>Considerações sobre a licença
Existem duas formas de licença do SQL Server em máquinas virtuais do Microsoft Azure:

1. Benefícios da mobilidade de licenças que fazem parte do Software Assurance. Para obter mais informações, consulte [mobilidade de licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Pague consoante a taxa de hora de máquinas virtuais do Azure com o SQL Server instalado. Consulte a secção "SQL Server" em [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Para obter mais informações sobre as taxas de licenciamento e atuais, veja [FAQ de licenciamento para máquinas virtuais](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server imagens disponíveis na galeria da Máquina Virtual do Azure
A Galeria de Máquina Virtual do Microsoft Azure inclui várias imagens que contêm o Microsoft SQL Server. O software instalado nas imagens de máquina virtual varia consoante a versão do sistema operativo e a versão do SQL Server. A lista de imagens disponíveis na galeria da máquina virtual do Azure é alterado frequentemente.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Imagem do SQL na Galeria VM do Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) O seguinte script do PowerShell devolve a lista de imagens do Azure que contenham "SQL-Server" no ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Para obter mais informações sobre as edições e funcionalidades suportadas pelo SQL Server, consulte o seguinte:

* [SQL Server Editions](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [Funcionalidades suportadas pelas edições do SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Recursos BI instalados nas imagens da galeria da Máquina Virtual de servidor de SQL
A tabela seguinte resume os recursos de Business Intelligence instalados nas imagens de galeria comuns do Microsoft Azure Virtual Machine para o SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Recurso de BI do SQL Server | Instalado na imagem de galeria | Notas |
| --- | --- | --- |
| **Modo nativo dos serviços de geração de relatórios** |Sim |Instalado, mas requer a configuração, incluindo o URL do Gestor de relatórios. Consulte a secção [configurar o Reporting Services](#configure-reporting-services). |
| **Modo SharePoint do Reporting Services** |Não |A imagem de galeria da Máquina Virtual do Microsoft Azure não inclui o SharePoint ou o SharePoint ficheiros de instalação. <sup>1</sup> |
| **Extração de dados e multidimensionais do Analysis Services (OLAP)** |Sim |Instalado e configurado como a instância do Analysis Services predefinida |
| **Tabela do Analysis Services** |Não |Suportado no SQL Server 2012, 2014 e 2016 imagens, mas ele não está instalado por predefinição. Instale outra instância do Analysis Services. Veja a secção instalar outros serviços do SQL Server e os recursos deste tópico. |
| **Analysis Services Power Pivot para o SharePoint** |Não |A imagem de galeria da Máquina Virtual do Microsoft Azure não inclui o SharePoint ou o SharePoint ficheiros de instalação. <sup>1</sup> |

<sup>1</sup> para obter informações adicionais sobre o SharePoint e máquinas virtuais do Azure, consulte [Microsoft Azure arquiteturas para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [em máquinas de virtuais de Azure do MicrosoftdeimplantaçãodoSharePoint](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Execute o seguinte comando do PowerShell para obter uma lista de serviços instalados que contêm "SQL" no nome do serviço.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Recomendações gerais e melhores práticas
* O mínimo para uma máquina virtual é o tamanho recomendado **A3** ao utilizar o SQL Server Enterprise Edition. O **A4** tamanho da máquina virtual é recomendado para implementações de BI do SQL Server do Analysis Services e Reporting Services.
  
    Para obter informações sobre os tamanhos VM atuais, consulte [tamanhos de Máquina Virtual do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* É recomendado para gestão de discos armazenar dados, registo e ficheiros de cópia de segurança em unidades que **C**: e **1!d**:. Por exemplo, criar discos de dados **i**: e **F**:.
  
  * A unidade de colocação em cache de política para a unidade de predefinição **C**: não é ideal para trabalhar com dados.
  * O **1!d**: unidade é uma unidade temporária que é utilizada principalmente para o ficheiro de paginação. O **1!d**: unidade não é persistente e não é guardada no armazenamento de Blobs. Reposição de tamanho de tarefas de gestão como uma alteração para a máquina virtual a **1!d**: unidade. É recomendado **não** utilizar o **1!d**: unidade de disco para ficheiros de base de dados, incluindo tempdb.
    
    Para obter mais informações sobre como criar e anexar discos, consulte [como anexar um disco de dados a uma Máquina Virtual](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Parar ou desinstalar os serviços que não planeja usar. Para o exemplo se a máquina virtual só é utilizada para o Reporting Services, parar ou desinstalar o Analysis Services e o SQL Server Integration Services. A imagem seguinte é um exemplo dos serviços que são iniciadas por predefinição.
  
    ![Serviços do SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > O motor de base de dados do SQL Server é obrigatório os cenários suportados de BI. Num único servidor topologia VM, o motor de base de dados deve estar em execução na mesma VM.
  
    Para obter mais informações, consulte o seguinte: [Desinstale o Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) e [desinstalar uma instância do Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Verifique **Windows Update** novo 'atualizações importantes". As imagens de Máquina Virtual do Microsoft Azure com frequência são atualizadas; No entanto atualizações importantes podem tornar-se disponível a partir **Windows Update** depois da imagem de VM foi atualizada pela última vez.

## <a name="example-deployment-topologies"></a>Topologias de implementação de exemplo
Seguem-se as implementações de exemplo que utilizam as máquinas virtuais do Microsoft Azure. As topologias nesses diagramas são apenas algumas das topologias possíveis que pode utilizar com os recursos de BI do SQL Server e máquinas virtuais do Microsoft Azure.

### <a name="single-virtual-machine"></a>Máquina Virtual Única
Analysis Services, Reporting Services, o motor de base de dados do SQL Server e origens de dados numa única máquina virtual.

![cenário de BI iass com 1 máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Duas máquinas virtuais
* Analysis Services, Reporting Services e o motor de base de dados do SQL Server numa única máquina virtual. Esta implementação inclui as bases de dados do servidor de relatórios.
* Origens de dados numa segunda VM. A segunda VM inclui o motor de base de dados do SQL Server como uma origem de dados.

![cenário de BI de iaas com 2 máquinas virtuais](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure misto – dados na base de dados SQL do Azure
* Analysis Services, Reporting Services e o motor de base de dados do SQL Server numa única máquina virtual. Esta implementação inclui as bases de dados do servidor de relatórios.
* Origem de dados é a base de dados SQL do Azure.

![vm de cenários de BI de iaas e AzureSQL como origem de dados](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Híbrida – dados no local
* Nesta implementação de exemplo do Analysis Services, Reporting Services e o motor de base de dados do SQL Server são executados numa única máquina virtual. A máquina virtual aloja as bases de dados do servidor de relatório. A máquina virtual está associada a um domínio no local através do sistema de rede Virtual do Azure ou alguns outro VPN túnel solução.
* Origem de dados está no local.

![BI iaas cenários no local e vm origens de dados](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuração de modo nativo do Reporting Services
Imagem da galeria da máquina virtual para o SQL Server inclui o modo nativo do Reporting Services instalado, no entanto, o servidor de relatórios não está configurado. Os passos nesta secção Configurar o servidor de relatórios do Reporting Services. Para obter mais informações sobre como configurar o modo nativo do Reporting Services, consulte [instalar o Reporting Services nativo modo Report Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Para obter conteúdo semelhante que utiliza scripts do Windows PowerShell para configurar o servidor de relatórios, consulte [utilize o PowerShell para criar um Azure VM com um servidor modo nativo relatório](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Ligar à máquina Virtual e iniciar o Reporting Services Configuration Manager
Existem dois fluxos de trabalho comuns para ligar a uma Máquina Virtual do Azure:

* Para se ligar, no, clique em, o nome da máquina virtual e, em seguida, clique em **Connect**. Abre-se de uma conexão de área de trabalho remota e o nome do computador é preenchido automaticamente.
  
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Ligar à máquina virtual com a conexão de área de trabalho remoto do Windows. Na interface do usuário da área de trabalho remoto:
  
  1. Tipo de **nome do serviço cloud** como o nome do computador.
  2. Tipo de dois pontos (:) e o número de porta que está configurado para o ponto de extremidade TCP remoto área de trabalho.
     
      Myservice.cloudapp.net:63133
     
      Para obter mais informações, consulte [o que é um serviço em nuvem?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Iniciar o Reporting Services Configuration Manager**

Na **Windows Server 2012/2016**:

1. Partir do **começar** ecrã, escreva **Reporting Services** para ver uma lista de aplicações.
2. Com o botão direito **Reporting Services Configuration Manager** e clique em **executar como administrador**.

Na **Windows Server 2008 R2**:

1. Clique em **começar**e, em seguida, clique em **todos os programas**.
2. Click **Microsoft SQL Server 2016**.
3. Clique em **ferramentas de configuração**.
4. Com o botão direito **Reporting Services Configuration Manager** e clique em **executar como administrador**.

Ou:

1. Clique em **Iniciar**.
2. Na **procurar programas e ficheiros** tipo caixa de diálogo **o reporting services**. Se a VM com o Windows Server 2012, escreva **o reporting services** na tela Iniciar do Windows Server 2012.
3. Com o botão direito **Reporting Services Configuration Manager** e clique em **executar como administrador**.
   
    ![pesquisa do ssrs configuration manager](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurar o Reporting Services
**Conta de serviço e o URL do serviço web:**

1. Verifique se o **nome do servidor** é o nome do servidor local e clique em **Connect**.
2. Tenha em atenção a em branco **nome de base de dados do servidor de relatório**. A base de dados é criado quando a configuração for concluída.
3. Verifique se o **estado do servidor de relatório** é **iniciado**. Se quiser verificar se o serviço no Gestor de servidores do Windows, o serviço está a **SQL Server Reporting Services** serviço do Windows.
4. Clique em **conta de serviço** e alterar a conta conforme necessário. Se a máquina virtual é utilizada num ambiente associado ao domínio, incorporada **ReportServer** conta é suficiente. Para obter mais informações sobre a conta de serviço, consulte [conta de serviço](https://msdn.microsoft.com/library/ms189964.aspx).
5. Clique em **URL do serviço Web** no painel esquerdo.
6. Clique em **aplicar** para configurar os valores predefinidos.
7. Tenha em atenção a **URLs de serviço Web do servidor de relatórios**. Tenha em atenção que a porta TCP predefinida é 80 e faz parte do URL. Um passo posterior, vai criar um ponto de final da Máquina Virtual de Azure Microsoft para a porta.
8. Na **resultados** painel, verifique se as ações foi concluídas com êxito.

**Base de dados:**

1. Clique em **base de dados** no painel esquerdo.
2. Clique em **alterar base de dados**.
3. Certifique-se **criar uma nova base de dados de servidor de relatório** está selecionada e, em seguida, clique em seguinte.
4. Certifique-se **nome do servidor** e clique em **Testar ligação**.
5. Se o resultado for **Testar ligação foi concluída com êxito**, clique em **OK** e, em seguida, clique em **seguinte**.
6. Tenha em atenção o nome de base de dados é **ReportServer** e o **modo de servidor de relatórios** é **nativo** , em seguida, clique em **seguinte**.
7. Clique em **próxima** sobre o **credenciais** página.
8. Clique em **próxima** sobre o **resumo** página.
9. Clique em **próxima** sobre o **o progresso e concluir** página.

**Web URL do Portal ou URL do Gestor de relatórios para o 2012 e 2014:**

1. Clique em **URL do Portal Web**, ou **URL do Gestor de relatórios** para 2014 e 2012, no painel esquerdo.
2. Clique em **Aplicar**.
3. Na **resultados** painel, verifique se as ações foi concluídas com êxito.
4. Clique em **saída**.

Para obter informações sobre as permissões do servidor de relatórios, consulte [conceder permissões no servidor de relatórios de modo nativo](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Navegue para o Gestor de relatórios local
Para verificar a configuração, navegue para o Gestor de relatórios na VM.

1. Na VM, inicie o Internet Explorer com privilégios de administrador.
2. Navegue para o http: \/ /localhost/relatórios na VM.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Para ligar ao portal web remoto ou o Gestor de relatórios para 2014 e 2012
Se pretender ligar-se para o web portal, ou o Gestor de relatórios para 2014 e 2012, na máquina virtual de um computador remoto, crie uma nova máquina virtual ponto de extremidade TCP. Por predefinição, o servidor de relatórios escuta pedidos de HTTP na **a porta 80**. Se configurar os URLs do servidor de relatório para utilizar uma porta diferente, tem de especificar esse número de porta nas seguintes instruções.

1. Crie um ponto final para a Máquina Virtual de porta TCP 80. Para obter mais informações consulte, o [pontos finais da Máquina Virtual e as portas de Firewall](#virtual-machine-endpoints-and-firewall-ports) seção deste documento.
2. Abra a porta 80 na firewall da máquina virtual.
3. Navegue para o portal web, ou um relatório manager, com a Máquina Virtual do Azure **nome de DNS** como o nome do servidor no URL. Por exemplo:
   
    **Servidor de relatórios**: http://uebi.cloudapp.net/reportserver  **Web portal**: http://uebi.cloudapp.net/reports
   
    [Configurar uma Firewall para acesso ao servidor de relatórios](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios para a Máquina Virtual do Azure
A tabela seguinte resume algumas das opções disponíveis para publicar relatórios existentes a partir de um computador no local para o servidor de relatórios alojados na máquina de Virtual do Microsoft Azure:

* **Report Builder**: A máquina virtual inclui o clique-uma vez a versão do Microsoft SQL Server Report Builder para SQL 2014 e 2012. Para iniciar o tempo do Report builder a primeira na máquina virtual com o SQL 2016:
  
  1. Comece o seu browser com privilégios administrativos.
  2. Navegue para o portal web, na máquina virtual e selecione o **transferir** ícone no canto superior direito.
  3. Selecione **Report Builder**.
     
     Para obter mais informações, consulte [iniciar o Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM:  SQL Server Data Tools está instalado na máquina virtual e podem ser utilizados para criar **projetos de servidor de relatório** e relatórios na máquina virtual. SQL Server Data Tools pode publicar os relatórios para o servidor de relatórios na máquina virtual.
* **Ferramentas de dados do SQL Server: Remoto**:  No seu computador local, crie um projeto do Reporting Services no SQL Server Data Tools que contém os relatórios do Reporting Services. Configure o projeto para ligar ao URL de serviço web.
  
    ![Propriedades do projeto SSDT para o projeto do SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Criar uma. VHD unidade de disco rígido que contém relatórios e, em seguida, carrega e ligar uma unidade.
  
  1. Criar uma. Unidade de disco rígido de VHD no seu computador local que contém os seus relatórios.
  2. Criar e instalar um certificado de gestão.
  3. Carregar o ficheiro VHD para o Azure com o cmdlet Add-AzureVHD [criar e carregar um VHD do Windows Server para o Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Anexe o disco à máquina virtual.

## <a name="install-other-sql-server-services-and-features"></a>Instalar outros serviços do SQL Server e funcionalidades
Para instalar serviços adicionais do SQL Server, como o Analysis Services no modo de tabela, execute o Assistente de configuração do SQL server. Os ficheiros de configuração estão no disco local da máquina virtual.

1. Clique em **começar** e, em seguida, clique em **todos os programas**.
2. Clique em **Microsoft SQL Server 2016**, **o Microsoft SQL Server 2014** ou **Microsoft SQL Server 2012** e, em seguida, clique em **ferramentas de configuração** .
3. Clique em **Centro de instalação do SQL Server**.

Or run C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe or C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Na primeira vez que executar a configuração do SQL Server, o mais arquivos de configuração podem ser transferidos e requerem um reinício da máquina virtual e um reinício de configuração do SQL Server.
> 
> Se precisar de personalizar repetidamente a imagem selecionada da máquina de Virtual do Microsoft Azure, considere criar sua própria imagem do SQL Server. Funcionalidade de SysPrep de serviços de análise foi ativada com o SQL Server 2012 SP1 CU2. Para obter mais informações, consulte [considerações para instalar o SQL Server usando o SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) e [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Para instalar o modo de tabela do Analysis Services
Os passos nesta secção **resumir** a instalação do modo de tabela do Analysis Services. Para obter mais informações, consulte o seguinte:

* [Instalar o Analysis Services no modo de tabela](https://msdn.microsoft.com/library/hh231722.aspx)
* [Modelagem de tabela (Tutorial da Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Para instalar o modo de tabela do Analysis Services:**

1. No Assistente de instalação do SQL Server, clique em **instalação** no painel esquerdo e clique em **instalação autônoma do novo SQL server ou adicionar funcionalidades a uma instalação existente**.
   
   * Se vir a **Procurar pasta**, procure c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full e, em seguida, clique em **Ok**.
2. Clique em **seguinte** na página de atualizações de produto.
3. Sobre o **tipo de instalação** página, selecione **executar uma nova instalação do SQL Server** e clique em **seguinte**.
4. Sobre o **função de configuração** página, clique em **instalação de funcionalidades do SQL Server**.
5. Sobre o **seleção de funcionalidades** página, clique em **Analysis Services**.
6. Na **configuração da instância** página, escreva um nome descritivo, como **tabela** no **instância com o nome** e **Id de instância** caixas de texto .
7. Sobre o **configuração do Analysis Services** página, selecione **modo de tabela**. Adicione o utilizador atual para a lista de permissões administrativas.
8. Conclua e feche o Assistente de instalação do SQL Server.

## <a name="analysis-services-configuration"></a>Configuração de serviços de análise
### <a name="remote-access-to-analysis-services-server"></a>Acesso remoto ao servidor do Analysis Services
Servidor do Analysis Services só suporta a autenticação do windows. Para aceder aos serviços de análise remotamente aplicações de cliente, como o SQL Server Management Studio ou o SQL Server Data Tools, a máquina virtual tem de ser associados ao seu domínio local, a utilizar redes virtuais do Azure. Para obter mais informações, consulte [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md).

R **instância predefinida** do Analysis Services escuta na porta TCP **2383**. Abra a porta na firewall de máquinas virtuais. Num cluster instância nomeada do Analysis Services também escuta na porta **2383**.

Para uma **instância nomeada** do Analysis Services, o serviço de Browser do SQL Server é necessário para gerir o acesso de porta. A configuração predefinida do SQL Server Browser é a porta **2382**.

Na firewall de máquinas virtuais, abra a porta **2382** e criar um nomeada porta da instância do Analysis Services estático.

1. Para verificar as portas que já estão em utilização na VM e que processo está a utilizar as portas, execute o seguinte comando com privilégios administrativos:
   
        netstat /ao
2. Utilize o SQL Server Management Studio para criar um serviço de análise estática valor com nome porta da instância, atualizando a "Porta" na tabela AS propriedades gerais da instância. Para obter mais informações, consulte o "utilizar uma porta fixa para uma predefinição ou instância nomeada" no [configurar a Firewall do Windows para permitir o acesso do Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Reinicie a instância de tabela do serviço Analysis Services.

Para obter mais informações consulte, o **pontos finais da Máquina Virtual e as portas de Firewall** seção deste documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Pontos finais da máquina virtual e as portas de Firewall
Esta secção resume os pontos finais de Máquina Virtual do Microsoft Azure para criar e portas para abrir nas firewalls de máquina virtual. A tabela seguinte resume os **TCP** portas para criar pontos finais para e as portas para o abrir na firewall de máquinas virtuais.

* Se estiver a utilizar uma única VM e os dois itens seguintes são verdadeiros, não é necessário criar pontos finais da VM e não é necessário abrir as portas na firewall na VM.
  
  * Não ligar remotamente para as funcionalidades do SQL Server na VM. Estabelecer uma conexão de área de trabalho remota à VM e acessar os recursos do SQL Server localmente na VM não é considerado uma ligação remota para os recursos do SQL Server.
  * Não associar a VM a um domínio no local através do sistema de rede Virtual do Azure ou outra solução de túnel de VPN.
* Se a máquina virtual não está associada a um domínio, mas pretende remotamente ligar-se às funcionalidades do SQL Server na VM:
  
  * Abra as portas na firewall na VM.
  * Crie pontos finais da máquina virtual para as portas indicadas (*).
* Se a máquina virtual estiver associada a um domínio através de um túnel VPN, tais como o sistema de rede Virtual do Azure, os pontos finais não são necessários. No entanto, abra as portas na firewall na VM.
  
  | Porta | Type | Descrição |
  | --- | --- | --- |
  | **80** |TCP |Servidor de relatórios de acesso remoto (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |Browser do SQL Server. Isto é necessário quando a VM na associado a um domínio. |
  | **2382** |TCP |Browser do SQL Server. |
  | **2383** |TCP |Instância de predefinida do SQL Server Analysis Services e as instâncias nomeadas em cluster. |
  | **definido pelo utilizador** |TCP |Criar do Analysis Services estático denominado porta da instância para um número de porta que escolher e, em seguida, desbloquear o número da porta na firewall. |

Para obter mais informações sobre a criação de pontos finais, consulte o seguinte:

* Criar pontos finais:[como configurar pontos finais para uma Máquina Virtual](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: Consulte a secção "Passos de configuração completa para ligar à máquina virtual usando SQL Server Management Studio" [aprovisionar uma Máquina Virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

O diagrama seguinte ilustra as portas para abrir na firewall VM para permitir o acesso remoto a recursos e componentes na VM.

![portas abrir para aplicativos de bi em VMs do Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Recursos
* Rever a política de suporte para software do servidor Microsoft no ambiente de Máquina Virtual do Azure. O tópico seguinte resume o suporte para funcionalidades, tais como o BitLocker, o Clustering de ativação pós-falha e balanceamento de carga na rede. [O suporte do software de servidores da Microsoft para máquinas de virtuais do Azure](https://support.microsoft.com/kb/2721672).
* [SQL Server em Descrição geral de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Aprovisionar uma Máquina Virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Como anexar um disco de dados a uma Máquina Virtual](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrar uma base de dados para o SQL Server numa VM do Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Determinar o modo de servidor de uma instância do Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modelagem multidimensional (Tutorial da Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centro de documentação do Azure](https://azure.microsoft.com/documentation/)
* [Utilizar o Power BI num ambiente híbrido](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Submeter comentários e informações de contato por meio de ligar do Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Conteúdo da Comunidade
* [Gestão de bases de dados SQL do Azure com o PowerShell](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

