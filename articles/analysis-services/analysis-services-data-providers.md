---
title: Bibliotecas de cliente necessárias para ligar ao Azure Analysis Services | Documentos da Microsoft
description: Descreve as bibliotecas de cliente necessárias para aplicações de cliente e ferramentas ligar o Azure Analysis Services
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bc8fae8f1278babd8c4c7402d86700ad78e3cb9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897423"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para ligar ao Azure Analysis Services

Bibliotecas de cliente são necessárias para aplicativos de cliente e ferramentas ligar aos servidores do Analysis Services. Aplicações de cliente do Microsoft, como o ambiente de trabalho do Power BI, Excel, SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) instalar todas as três bibliotecas de cliente e atualizá-las juntamente com as atualizações de aplicativo regular. Em alguns casos, terá de instalar as versões mais recentes das bibliotecas de cliente. Aplicações cliente personalizadas também necessitam de bibliotecas de cliente são instaladas.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Transferir as bibliotecas de cliente mais recente (programa de instalação do Windows)  

|Transferência  |Versão do produto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.11.19    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.11.19      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.15.0.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.15.0.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO e ADOMD (pacotes de NuGet)

Bibliotecas de cliente do Analysis Services Management Objects (AMO) e ADOMD estão disponíveis como pacotes podem ser instaladas a partir [NuGet.org](https://www.nuget.org/). Recomenda-se que migrar para referências de NuGet em vez de usar o Windows Installer. 

|Pacote  | Versão do produto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.15.0.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.15.0.0      |

Assemblies do pacote NuGet AssemblyVersion siga semântico controle de versão: PRINCIPAIS. PEQUENAS. PATCH. Referências de NuGet carregar a versão esperada, mesmo se houver uma versão diferente no GAC (o que resulta da instalação com MSI). PATCH é incrementado para cada versão. Versões do AMO e ADOMD são mantidas em sincronia.

## <a name="understanding-client-libraries"></a>Compreender as bibliotecas de cliente

Analysis Services utiliza três bibliotecas de cliente, também conhecido como provedores de dados. ADOMD.NET e Analysis Services Management Objects (AMO) são bibliotecas de cliente gerenciado. OLE DB fornecedor do Analysis Services (MSOLAP DLL) é uma biblioteca de cliente nativo. Normalmente, todos os três são instalados ao mesmo tempo. **O Azure Analysis Services requer as versões mais recentes de todas as três bibliotecas**. 

Aplicações de cliente da Microsoft, como o ambiente de trabalho do Power BI e Excel instalar todas as três bibliotecas de cliente e atualização-los quando existem novas versões. Consoante a versão ou a frequência de atualizações, algumas bibliotecas de cliente não podem ser as versões mais recentes do Azure Analysis Services precisa. O mesmo se aplica a aplicações personalizadas ou a outras interfaces, como sCmd, TOM ou ADOMD.NET. Esses aplicativos exigem manual ou programaticamente a instalar as bibliotecas. As bibliotecas de cliente para a instalação manual estão incluídas nos pacotes de funcionalidades do SQL Server como pacotes distribuíveis. No entanto, estas bibliotecas de cliente estão associadas a versão do SQL Server e podem não ser a versão mais recente.  

Bibliotecas de cliente para ligações de cliente são diferentes de fornecedores de dados necessárias para ligar-se de um servidor Azure Analysis Services a uma origem de dados. Para saber mais sobre ligações de origem de dados, veja [ligações de origem de dados](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipos de biblioteca de cliente

### <a name="analysis-services-ole-db-provider-msolap"></a>Fornecedor do Analysis Services OLE DB (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) é a biblioteca de cliente nativo para ligações de base de dados do Analysis Services. Ele é usado indiretamente, ADOMD.NET e AMO, delegando pedidos de ligação para o fornecedor de dados. Também pode chamar o fornecedor de OLE DB diretamente a partir do código da aplicação.  
  
 OLE DB fornecedor do Analysis Services é instalado automaticamente pela maioria das ferramentas e aplicativos cliente usados para acessar bancos de dados do Analysis Services. Tem de ser instalado em computadores utilizados para aceder a dados do Analysis Services.  
  
 Fornecedores de OLE DB, muitas vezes, são especificados em cadeias de ligação. Uma cadeia de ligação do Analysis Services utiliza uma nomenclatura diferente para consultar o fornecedor de OLE DB: MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 AMO é uma biblioteca de cliente gerido utilizada para a administração de servidor e a definição de dados. É instalado e utilizado por ferramentas e aplicativos de cliente. Por exemplo, o SQL Server Management Studio (SSMS) utiliza o AMO para ligar ao Analysis Services. Uma ligação com o AMO é normalmente mínima, consistindo em `"data source=\<servername>"`. Após uma conexão é estabelecida, utilizar a API para trabalhar com coleções da base de dados e objetos principais. SSDT e SSMS usar AMO para ligar a uma instância do Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET é uma biblioteca de cliente de dados geridos usada para consultar dados do Analysis Services. É instalado e utilizado por ferramentas e aplicativos de cliente. 
  
 Ao ligar a uma base de dados, as propriedades de cadeia de ligação para todas as três bibliotecas são semelhantes. Praticamente qualquer cadeia de ligação definidas para ADOMD.NET utilizando [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) também funciona para AMO e o Analysis Services OLE DB fornecedor (MSOLAP). Para obter mais informações, consulte [propriedades de cadeia de ligação &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Como determinar a versão da biblioteca de cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Aceda a `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Se tiver mais do que uma pasta, selecione o número mais alto.
  
2.  Com o botão direito **msolap.dll** > **propriedades** > **detalhes**. Se o nome de ficheiro for msolap140.dll, ele é mais antigo que a versão mais recente e deve ser atualizado.
    
    ![Detalhes da biblioteca de cliente](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Se tiver mais do que uma pasta, selecione o número mais alto.
2. Com o botão direito **analysisservices** > **propriedades** > **detalhes**.  

### <a name="adomd"></a>ADOMD

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Se tiver mais do que uma pasta, selecione o número mais alto.
2. Com o botão direito **Microsoft.AnalysisServices.AdomdClient** > **propriedades** > **detalhes**.  


## <a name="next-steps"></a>Passos Seguintes
[Ligar com o Excel](analysis-services-connect-excel.md)    
[Ligar com o Power BI](analysis-services-connect-pbi.md)
