---
title: Utilizar o ReportViewer num Web Site | Documentos da Microsoft
description: Este tópico descreve como criar um site da Web do Microsoft Azure com o controle ReportViewer do Visual Studio, que exibe um relatório armazenado numa máquina de Virtual do Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580073"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Utilizar o ReportViewer num Web Site Alojado no Azure
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Pode criar um site da Web do Microsoft Azure com o controle ReportViewer do Visual Studio, que exibe um relatório armazenado numa máquina de Virtual do Microsoft Azure. O controle ReportViewer é num aplicativo Web que criar usando o modelo de aplicativo ASP.NET Web.

> [!IMPORTANT]
> Os modelos de aplicativo Web ASP.NET MVC não suportam o controle ReportViewer.

Para incorporar o ReportViewer em seu site da Web do Microsoft Azure, terá de concluir as seguintes tarefas.

* **Adicionar** Assemblies para o pacote de implementação
* **Configurar** autenticação e autorização
* **Publicar** a aplicação ASP.NET Web no Azure

## <a name="prerequisites"></a>Pré-requisitos
Consulte a secção "recomendação geral e melhores práticas" na [SQL Server Business Intelligence em máquinas de virtuais do Azure](../classic/ps-sql-bi.md).

> [!NOTE]
> Controles ReportViewer são entregues com o Visual Studio Standard Edition ou superior. Se estiver a utilizar o Web Developer Express Edition, tem de instalar o [o tempo de execução do MICROSOFT REPORT VIEWER 2012](https://www.microsoft.com/download/details.aspx?id=35747) a usar os recursos de tempo de execução do ReportViewer.
>
> ReportViewer configurado no modo de processamento local não é suportada no Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Adicionar Assemblies para o pacote de implementação
Ao alojar ASP.NET aplicações no local, os assemblies do ReportViewer, normalmente, são instalados diretamente no cache de global assembly (GAC) do servidor IIS durante a instalação do Visual Studio e podem ser acedidos diretamente pela aplicação. No entanto, quando hospeda o aplicativo do ASP.NET na cloud, Microsoft Azure não permite nada para ser instalado no GAC, pelo que deve certificar-se de que os assemblies do ReportViewer estão disponíveis localmente para a sua aplicação. Pode fazê-lo ao adicionar referências aos mesmos no seu projeto e configurá-las para ser copiado localmente.

No modo de processamento remoto, o controle ReportViewer utiliza dos assemblies a seguir:

* **Microsoft.ReportViewer.WebForms.dll**: Contém o código de ReportViewer, que tem de utilizar o ReportViewer num sua página. Uma referência para esse assembly é adicionada ao seu projeto ao soltar um controle ReportViewer numa página ASP.NET em seu projeto.
* **Microsoft.ReportViewer.Common.dll**: Contém as classes usadas pelo controle ReportViewer no tempo de execução. Ele não é automaticamente adicionado ao seu projeto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Para adicionar uma referência ao Microsoft.ReportViewer.Common
* Com o botão direito do seu projeto **referências** nó e selecione **Add Reference**, selecione o assembly na guia .NET e clique em **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Para fazer com que os assemblies localmente acessível pela sua aplicação ASP.NET
1. Na **referências** pasta, clique em Microsoft.ReportViewer.Common assembly para que as respetivas propriedades são apresentados no painel de propriedades.
2. No painel de propriedades, defina **Copy Local** como True.
3. Repita os passos 1 e 2 para Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Para obter o pacote de idiomas do ReportViewer
1. Instalar o pacote redistribuível do tempo de execução do Microsoft Report Viewer 2012 adequado partir [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=317386).
2. Selecione o idioma na lista pendente e a página é redirecionada para a página correspondente do Centro de download.
3. Clique em **transferir** para iniciar o download de ReportViewerLP.exe.
4. Depois de transferir ReportViewerLP.exe, clique em **execute** para instalar imediatamente ou clique em **guardar** guardá-la no seu computador. Se clicar **guardar**, lembre-se o nome da pasta onde guardar o ficheiro.
5. Localize a pasta onde guardou o ficheiro. Com o botão direito ReportViewerLP.exe, clique em **executar como administrador**e, em seguida, clique em **Sim**.
6. Depois de executar ReportViewerLP.exe, verá o c:\windows\assembly tem os ficheiros de recursos **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Configurar para o localizados controle ReportViewer
1. Baixe e instale o pacote redistribuível do tempo de execução do Microsoft Report Viewer 2012 ao seguir as instruções especificadas acima.
2. Crie \<linguagem\> pasta no projeto e copie o assembly do recurso associado ficheiros lá. Os ficheiros de assemblagem de recurso seja copiado são: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selecione os ficheiros de assemblagem de recurso e, no painel de propriedades, defina **Copy to Output Directory** para "**Copiar sempre**".
3. Defina o Culture e UICulture para o projeto web. Para obter mais informações sobre como definir a cultura e a cultura da interface do Usuário para uma página da ASP.NET Web, consulte [como: Defina a cultura e a cultura da IU para globalização de página da Web do ASP.NET](https://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configuração da autenticação e autorização
O ReportViewer tem de utilizar as credenciais corretas para autenticar com o servidor de relatórios e as credenciais tem de estar autorizadas pelo servidor de relatórios para aceder aos relatórios que pretende. Para informações sobre autenticação, consulte o white paper [relatório do Reporting Services controlo do Visualizador e servidores de relatórios de máquina virtual com base do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicar a aplicação ASP.NET Web no Azure
Para obter instruções sobre a publicação de uma aplicação ASP.NET Web no Azure, consulte [como: Migrar e publicar uma aplicação Web para o Azure a partir do Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [comece com aplicações Web e ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Se o comando Adicionar projeto de implementação do Azure ou adicionar projeto de serviço de nuvem do Azure não aparecer no menu de atalho no Explorador de soluções, terá de alterar a estrutura de destino para o projeto para o .NET Framework 4.
>
> Os dois comandos oferecem essencialmente a mesma funcionalidade. Um ou o outro comando aparecerá no menu de atalho consoante a versão do SDK do Microsoft Azure que instalou.
>
>

## <a name="resources"></a>Recursos
[Relatórios da Microsoft](https://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence em Máquinas Virtuais do Azure](../classic/ps-sql-bi.md)

[Utilizar o PowerShell para Criar uma VM do Azure com um Servidor de Relatórios no Modo Nativo](../classic/ps-sql-report.md)
