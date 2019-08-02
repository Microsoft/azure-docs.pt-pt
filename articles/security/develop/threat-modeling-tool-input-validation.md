---
title: Validação de entrada-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: mitigações para ameaças expostas no Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: f443bf3111d2ab97874bdc62ec1370d17e2fc406
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728064"
---
# <a name="security-frame-input-validation--mitigations"></a>Quadro de segurança: Validação de entrada | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicativo Web** | <ul><li>[Desabilitar o script XSLT para todas as transformações usando folhas de estilo não confiáveis](#disable-xslt)</li><li>[Verifique se cada página que pode conter conteúdo controlável do usuário opta por detecção automática de MIME](#out-sniffing)</li><li>[Proteger ou desabilitar a resolução de entidade XML](#xml-resolution)</li><li>[Aplicativos que utilizam http. sys executam a verificação de canonização de URL](#app-verification)</li><li>[Garantir que os controles apropriados estejam em vigor ao aceitar arquivos de usuários](#controls-users)</li><li>[Verifique se os parâmetros de tipo seguro são usados no aplicativo Web para acesso a dados](#typesafe)</li><li>[Usar classes de associação de modelo separadas ou listas de filtro de associação para evitar vulnerabilidades de atribuição em massa do MVC](#binding-mvc)</li><li>[Codificar saída da Web não confiável antes da renderização](#rendering)</li><li>[Executar validação e filtragem de entrada em todas as propriedades de modelo de tipo de cadeia de caracteres](#typemodel)</li><li>[A limpeza deve ser aplicada em campos de formulário que aceitam todos os caracteres, por exemplo, editor de Rich Text](#richtext)</li><li>[Não atribua elementos DOM a coletores que não têm codificação embutida](#inbuilt-encode)</li><li>[Validar que todos os redirecionamentos dentro do aplicativo sejam fechados ou concluídos com segurança](#redirect-safe)</li><li>[Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos por métodos do controlador](#string-method)</li><li>[Definir o tempo limite de limite superior para o processamento de expressão regular para evitar DoS devido a expressões regulares inadequadas](#dos-expression)</li><li>[Evite usar HTML. RAW em exibições do Razor](#html-razor)</li></ul> | 
| **Base de Dados** | <ul><li>[Não usar consultas dinâmicas em procedimentos armazenados](#stored-proc)</li></ul> |
| **API Web** | <ul><li>[Verifique se a validação do modelo é feita nos métodos da API Web](#validation-api)</li><li>[Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos por métodos de API Web](#string-api)</li><li>[Verifique se os parâmetros de tipo seguro são usados na API Web para acesso a dados](#typesafe-api)</li></ul> | 
| **BD de documentos do Azure** | <ul><li>[Usar consultas SQL parametrizadas para Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Validação de entrada do WCF por meio de associação de esquema](#schema-binding)</li><li>[WCF-validação de entrada por meio de inspetores de parâmetro](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Desabilitar o script XSLT para todas as transformações usando folhas de estilo não confiáveis

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Segurança XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [Propriedade XsltSettings. XsltSettings](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Passos** | O XSLT dá suporte a scripts dentro de folhas `<msxml:script>` de estilo usando o elemento. Isso permite que as funções personalizadas sejam usadas em uma transformação XSLT. O script é executado sob o contexto do processo que executa a transformação. O script XSLT deve ser desabilitado quando estiver em um ambiente não confiável para impedir a execução de código não confiável. *Se estiver usando o .NET:* O script XSLT está desabilitado por padrão; no entanto, você deve garantir que ele não tenha sido explicitamente habilitado `XsltSettings.EnableScript` por meio da propriedade.|

### <a name="example"></a>Exemplo 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se você estiver usando o MSXML 6,0, o script XSLT será desabilitado por padrão; no entanto, você deve garantir que ele não tenha sido explicitamente habilitado por meio da propriedade de objeto DOM XML AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se você estiver usando o MSXML 5 ou inferior, o script XSLT será habilitado por padrão e você deverá desabilitá-lo explicitamente. Defina a propriedade de objeto XML DOM AllowXsltScript como false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Verifique se cada página que pode conter conteúdo controlável do usuário opta por detecção automática de MIME

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Segurança do IE8 parte V – proteção abrangente](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Passos** | <p>Para cada página que pode conter conteúdo controlável do usuário, você deve usar o cabeçalho `X-Content-Type-Options:nosniff`http. Para atender a esse requisito, você pode definir a página de cabeçalho necessária por página somente para as páginas que podem conter conteúdo controlável pelo usuário ou pode defini-las globalmente para todas as páginas no aplicativo.</p><p>Cada tipo de arquivo entregue de um servidor Web tem um [tipo MIME](https://en.wikipedia.org/wiki/Mime_type) associado (também chamado de *tipo de conteúdo*) que descreve a natureza do conteúdo (ou seja, imagem, texto, aplicativo, etc.)</p><p>O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que seu conteúdo não deve ser sniffed MIME. Esse cabeçalho é projetado para mitigar ataques de detecção de MIME. O suporte para esse cabeçalho foi adicionado no Internet Explorer 8 (IE8)</p><p>Somente os usuários do Internet Explorer 8 (IE8) se beneficiarão de opções de tipo de conteúdo X. As versões anteriores do Internet Explorer atualmente não respeitam o cabeçalho X-Content-Type-Options</p><p>O Internet Explorer 8 (e posterior) são os únicos navegadores principais para implementar um recurso de recusa de detecção de MIME. Se e quando outros navegadores principais (Firefox, Safari, Chrome) implementarem recursos semelhantes, essa recomendação será atualizada para incluir também a sintaxe para esses navegadores</p>|

### <a name="example"></a>Exemplo
Para habilitar o cabeçalho necessário globalmente para todas as páginas no aplicativo, você pode executar um dos seguintes procedimentos: 

* Adicione o cabeçalho no arquivo Web. config se o aplicativo for hospedado pelo Serviços de Informações da Internet (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Adicionar o cabeçalho por meio do aplicativo\_global BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementar o módulo HTTP personalizado 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Você pode habilitar o cabeçalho necessário somente para páginas específicas adicionando-o a respostas individuais: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Proteger ou desabilitar a resolução de entidade XML

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Expansão de entidade XML](https://capec.mitre.org/data/definitions/197.html), [ataques de negação de serviço XML e defesas](https://msdn.microsoft.com/magazine/ee335713.aspx), [visão geral de segurança do MSXML](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [práticas recomendadas para proteger o código MSXML](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [referência de protocolo NSXMLParserDelegate](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [resolução de referências externas ](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Passos**| <p>Embora não seja amplamente usado, há um recurso do XML que permite que o analisador XML expanda entidades de macro com valores definidos no próprio documento ou em fontes externas. Por exemplo, o documento pode definir uma entidade "CompanyName" com o valor "Microsoft", de forma que sempre que o texto&companyname;"" seja exibido no documento, ele seja automaticamente substituído pelo texto Microsoft. Ou, o documento pode definir uma entidade "MSFTStock" que faz referência a um serviço Web externo para buscar o valor atual do estoque da Microsoft.</p><p>Em seguida, sempre&MSFTStock;que "" aparecer no documento, ele será substituído automaticamente pelo preço atual da ação. No entanto, essa funcionalidade pode ser usada para criar condições de negação de serviço (DoS). Um invasor pode aninhar várias entidades para criar um bomba XML de expansão exponencial que consome toda a memória disponível no sistema. </p><p>Como alternativa, ele pode criar uma referência externa que transmite de volta uma quantidade infinita de dados ou que simplesmente trava o thread. Como resultado, todas as equipes devem desabilitar totalmente a resolução de entidade XML interna e/ou externa se seu aplicativo não a usar, ou limitar manualmente a quantidade de memória e o tempo que o aplicativo pode consumir para a resolução de entidade se essa funcionalidade for absolutamente necessário. Se a resolução da entidade não for exigida pelo seu aplicativo, desabilite-a. </p>|

### <a name="example"></a>Exemplo
Para .NET Framework código, você pode usar as seguintes abordagens:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observe que o valor padrão de `ProhibitDtd` em `XmlReaderSettings` `XmlTextReader` é true, mas é false. Se você estiver usando XmlReaderSettings, não será necessário definir ProhibitDtd como true explicitamente, mas é recomendável para fins de segurança. Observe também que a classe XmlDocument permite a resolução de entidade por padrão. 

### <a name="example"></a>Exemplo
Para desabilitar a resolução de entidade para XmlDocuments, `XmlDocument.Load(XmlReader)` use a sobrecarga do método Load e defina as propriedades apropriadas no argumento XmlReader para desabilitar a resolução, conforme ilustrado no código a seguir: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exemplo
Se desabilitar a resolução de entidade não for possível para seu aplicativo, defina a propriedade XmlReaderSettings. MaxCharactersFromEntities com um valor razoável de acordo com as necessidades do seu aplicativo. Isso limitará o impacto de ataques de DoS de expansão exponencial potencial. O código a seguir fornece um exemplo dessa abordagem: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exemplo
Se você precisar resolver entidades embutidas, mas não precisar resolver entidades externas, defina a propriedade XmlReaderSettings. XmlResolver como NULL. Por exemplo: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observe que no MSXML6, ProhibitDTD é definido como true (desabilitando o processamento de DTD) por padrão. Para código Apple OSX/iOS, há dois Analisadores XML que você pode usar: NSXMLParser e libXML2. 

## <a id="app-verification"></a>Aplicativos que utilizam http. sys executam a verificação de canonização de URL

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Qualquer aplicativo que usa http. sys deve seguir estas diretrizes:</p><ul><li>Limite o comprimento da URL para não mais de 16.384 caracteres (ASCII ou Unicode). Esse é o comprimento de URL máximo absoluto com base na configuração padrão de Serviços de Informações da Internet (IIS) 6. Os sites devem buscar um comprimento menor do que isso, se possível</li><li>Use as classes de e/s de arquivo .NET Framework padrão (como FileStream), pois elas aproveitarão as regras de canonização no .NET FX</li><li>Criar explicitamente uma lista de permissões de nomes de os conhecidos</li><li>Rejeite explicitamente os tipos de arquivo conhecidos que você não atenderá aos rejeições do UrlScan: exe, bat, cmd, com, htw, ida, idq, htr, IDC, shtm [l], STM, Printer, ini, pol, arquivos dat</li><li>Capture as seguintes exceções:<ul><li>System. ArgumentException (para nomes de dispositivo)</li><li>System. NotSupportedException (para fluxos de dados)</li><li>System. IO. FileNotFoundException (para nomes de fileescape inválidos)</li><li>System. IO. DirectoryNotFoundException (para diretórios com escape inválidos)</li></ul></li><li>*Não* chame para APIs de e/s de arquivo do Win32. Em uma URL inválida, retorna normalmente um erro 400 para o usuário e registra o erro real.</li></ul>|

## <a id="controls-users"></a>Garantir que os controles apropriados estejam em vigor ao aceitar arquivos de usuários

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Carregamento de arquivo irrestrito](https://www.owasp.org/index.php/Unrestricted_File_Upload), [tabela de assinatura de arquivo](https://www.garykessler.net/library/file_sigs.html) |
| **Passos** | <p>Os arquivos carregados representam um risco significativo aos aplicativos.</p><p>A primeira etapa em muitos ataques é obter um código para o sistema ser atacado. Em seguida, o ataque precisa apenas encontrar uma maneira de executar o código. O uso de um upload de arquivo ajuda o invasor a realizar a primeira etapa. As consequências do carregamento de arquivo irrestrito podem variar, incluindo tomada de sistema completo, um sistema de arquivos sobrecarregado ou banco de dados, ataques de encaminhamento a sistemas de back-end e desfiguração simples.</p><p>Depende do que o aplicativo faz com o arquivo carregado e, especialmente, onde ele é armazenado. A validação do lado do servidor de carregamentos de arquivo está ausente. Os seguintes controles de segurança devem ser implementados para a funcionalidade de carregamento de arquivo:</p><ul><li>Verificação de extensão de arquivo (somente um conjunto válido de tipo de arquivo permitido deve ser aceito)</li><li>Limite máximo de tamanho de arquivo</li><li>O arquivo não deve ser carregado no Webroot; o local deve ser um diretório em uma unidade que não seja do sistema</li><li>A Convenção de nomenclatura deve ser seguida, de modo que o nome do arquivo carregado tenha alguma aleatoriedade, para evitar substituições de arquivo</li><li>Os arquivos devem ser verificados quanto ao antivírus antes de gravar no disco</li><li>Verifique se o nome do arquivo e quaisquer outros metadados (por exemplo, o caminho do arquivo) são validados para caracteres mal-intencionados</li><li>A assinatura de formato de arquivo deve ser verificada, para impedir que um usuário carregue um arquivo mascarado (por exemplo, carregando um arquivo exe alterando a extensão para txt)</li></ul>| 

### <a name="example"></a>Exemplo
Para o último ponto relacionado à validação de assinatura de formato de arquivo, consulte a classe abaixo para obter detalhes: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>Verifique se os parâmetros de tipo seguro são usados no aplicativo Web para acesso a dados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Se você usar a coleção Parameters, o SQL tratará que a entrada é como um valor literal em vez de código executável. A coleção Parameters pode ser usada para impor restrições de tipo e comprimento em dados de entrada. Os valores fora do intervalo disparam uma exceção. Se os parâmetros SQL de tipo seguro não forem usados, os invasores poderão executar ataques de injeção inseridos na entrada não filtrada.</p><p>Use parâmetros de tipo seguro ao construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com entrada não filtrada. Você pode usar parâmetros de tipo seguro com procedimentos armazenados e instruções SQL dinâmicas. Os parâmetros são tratados como valores literais pelo banco de dados e não como código executável. Os parâmetros também são verificados quanto ao tipo e comprimento.</p>|

### <a name="example"></a>Exemplo 
O código a seguir mostra como usar parâmetros de tipo seguro com o SqlParameterCollection ao chamar um procedimento armazenado. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
No exemplo de código anterior, o valor de entrada não pode ter mais de 11 caracteres. Se os dados não estiverem de acordo com o tipo ou o comprimento definido pelo parâmetro, a classe SqlParameter lançará uma exceção. 

## <a id="binding-mvc"></a>Usar classes de associação de modelo separadas ou listas de filtro de associação para evitar vulnerabilidades de atribuição em massa do MVC

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Atributos de metadados](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [vulnerabilidade de segurança de chave pública e mitigação](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [guia completo para atribuição em massa no ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [introdução com o EF usando MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Passos** | <ul><li>**Quando devo procurar vulnerabilidades de excesso de postagem?-** As vulnerabilidades de excesso de postagem podem ocorrer em qualquer lugar em que você associa classes de modelo da entrada do usuário. Estruturas como o MVC podem representar dados do usuário em classes personalizadas do .NET, incluindo POCOs (objetos comuns do CLR). O MVC preenche automaticamente essas classes de modelo com dados da solicitação, fornecendo uma representação conveniente para lidar com a entrada do usuário. Quando essas classes incluem propriedades que não devem ser definidas pelo usuário, o aplicativo pode ser vulnerável a ataques de excesso de postagem, o que permite que o usuário controle os dados que o aplicativo nunca pretendia. Como a associação de modelo do MVC, as tecnologias de acesso ao banco de dados, como Mapeadores de objeto/relacionais, como o Entity Framework geralmente também dão suporte ao uso de objetos POCO para representar dados do banco Essas classes de modelo de dados fornecem a mesma conveniência para lidar com os dados do banco que o MVC faz para lidar com a entrada do usuário. Como o MVC e o banco de dados dão suporte a modelos semelhantes, como objetos POCO, parece fácil reutilizar as mesmas classes para ambas as finalidades. Essa prática falha em preservar a separação de preocupações, e é uma área comum em que propriedades não intencionais são expostas à associação de modelo, permitindo ataques de excesso de postagem.</li><li>**Por que não devo usar minhas classes de modelo de banco de dados não filtradas como parâmetros para minhas ações do MVC?-** Como a associação de modelo MVC associará qualquer coisa nessa classe. Mesmo que os dados não apareçam no modo de exibição, um usuário mal-intencionado pode enviar uma solicitação HTTP com esses dados incluídos, e o MVC o ligará de forma feliz porque sua ação diz que a classe de banco de dados é a maneira de que ele deve aceitar para a entrada do usuário.</li><li>**Por que devo me preocupar com a forma usada para a associação de modelo?-** Usar a associação de modelo MVC do ASP.NET com modelos excessivamente amplos expõe um aplicativo para ataques de excesso de postagem. O excesso de postagens pode permitir que os invasores alterem dados de aplicativos além do que o desenvolvedor pretendia, como substituir o preço de um item ou os privilégios de segurança de uma conta. Os aplicativos devem usar modelos de associação específicos de ação (ou listas de filtros de propriedade permitidas específicas) para fornecer um contrato explícito para a entrada não confiável a ser permitida via Associação de modelo.</li><li>**Está tendo modelos de ligação separados apenas duplicando o código?-** Não, é uma questão de separação de preocupações. Se você reutilizar modelos de banco de dados em métodos de ação, você está dizendo que qualquer propriedade (ou subpropriedade) nessa classe pode ser definida pelo usuário em uma solicitação HTTP. Se isso não for o que você deseja que o MVC faça, será necessária uma lista de filtros ou uma forma de classe separada para mostrar ao MVC quais dados podem vir da entrada do usuário.</li><li>**Se eu tiver modelos de associação separados para entrada do usuário, preciso duplicar todos os atributos de anotação de dados?-** Não necessariamente. Você pode usar MetadataTypeAttribute na classe de modelo de banco de dados para vincular aos metadados em uma classe de associação de modelo. Apenas observe que o tipo referenciado pelo MetadataTypeAttribute deve ser um subconjunto do tipo de referência (ele pode ter menos Propriedades, mas não mais).</li><li>**Mover dados para frente e para trás entre modelos de entrada e modelos de banco do usuário é entediante. Posso simplesmente copiar todas as propriedades usando reflexão? -** Sim. As únicas propriedades que aparecem nos modelos de associação são aquelas que você determinou para serem seguras para entrada do usuário. Não há motivo de segurança que impeça o uso de reflexão para copiar todas as propriedades que existem em comum entre esses dois modelos.</li><li>**E quanto a [BIND (exclude = "â € ¦")]. Posso usar isso em vez de ter modelos de ligação separados? -** Essa abordagem não é recomendada. Usar [BIND (exclude = "â € ¦")] significa que qualquer nova propriedade é ligável por padrão. Quando uma nova propriedade é adicionada, há uma etapa extra para lembrar-se de manter as coisas seguras, em vez de ter o design seguro por padrão. Dependendo da verificação do desenvolvedor dessa lista sempre que uma propriedade for adicionada, será arriscado.</li><li>**É [BIND (include = "â € ¦")] útil para operações de edição?-** Não. [BIND (include = "â € ¦")] é adequado somente para operações de estilo de inserção (adicionando novos dados). Para operações de estilo de atualização (revisando dados existentes), use outra abordagem, como ter modelos de associação separados ou passar uma lista explícita de propriedades permitidas para UpdateModel ou TryUpdateModel. Adicionar um atributo [BIND (include = "â € ¦")] em uma operação de edição significa que o MVC criará uma instância de objeto e definirá apenas as propriedades listadas, deixando todos os outros com seus valores padrão. Quando os dados são persistidos, ele substituirá totalmente a entidade existente, redefinindo os valores de todas as propriedades omitidas para seus padrões. Por exemplo, se ISAdmin foi omitido de um atributo [BIND (include = "â € ¦")] em uma operação de edição, qualquer usuário cujo nome foi editado por essa ação seria redefinido para ISAdmin = false (qualquer usuário editado perderá o status do administrador). Se você quiser impedir atualizações para determinadas propriedades, use uma das outras abordagens acima. Observe que algumas versões das ferramentas MVC geram classes de controlador com [BIND (include = "â € ¦")] em Edit Actions e sugerem que a remoção de uma propriedade dessa lista impedirá ataques de excesso de postagem. No entanto, conforme descrito acima, essa abordagem não funciona como pretendida e, em vez disso, redefinirá todos os dados nas propriedades omitidas para seus valores padrão.</li><li>**Para operações de criação, há qualquer restrição usando [BIND (include = "â € ¦")] em vez de modelos de associação separados?-** Ok. Primeiro, essa abordagem não funciona para cenários de edição, exigindo a manutenção de duas abordagens separadas para atenuar todas as vulnerabilidades de excesso de postagem. Segundo, os modelos de associação separados impõem a separação de preocupações entre a forma usada para entrada do usuário e a forma usada para persistência, algo [BIND (include = "â € ¦")] não faz. Terceiro, observe que [BIND (include = "â € ¦")] só pode manipular propriedades de nível superior; Você não pode permitir apenas partes de subpropriedades (como "Details.Name") no atributo. Finalmente, e talvez o mais importante, usando [BIND (include = "â € ¦")] adiciona uma etapa extra que deve ser lembrada sempre que a classe for usada para associação de modelo. Se um novo método de ação for associado à classe de dados diretamente e se esquecer de incluir um atributo [BIND (include = "â € ¦")], ele poderá ser vulnerável a ataques de excesso de postagem, de modo que a abordagem [BIND (include = "â € ¦")] é um pouco menos segura por padrão. Se você usar [BIND (include = "â € ¦")], tome cuidado sempre para especificá-lo toda vez que suas classes de dados aparecerem como parâmetros do método de ação.</li><li>**Para operações de criação, e quanto à colocação do atributo [BIND (include = "â € ¦")] na própria classe de modelo? Não essa abordagem evita a necessidade de se lembrar de colocar o atributo em cada método de ação? -** Essa abordagem funciona em alguns casos. Usar [BIND (include = "â € ¦")] no próprio tipo de modelo (em vez de nos parâmetros de ação usando essa classe), evita a necessidade de se lembrar de incluir o atributo [BIND (include = "â € ¦")] em cada método de ação. Usar o atributo diretamente na classe efetivamente cria uma área de superfície separada dessa classe para fins de associação de modelo. No entanto, essa abordagem permite apenas uma forma de associação de modelo por classe de modelo. Se um método de ação precisar permitir a associação de modelo de um campo (por exemplo, uma ação somente de administrador que atualiza as funções de usuário) e outras ações precisarem impedir a associação de modelo desse campo, essa abordagem não funcionará. Cada classe só pode ter uma forma de associação de modelo; se ações diferentes precisarem de formas de associação de modelo diferentes, elas precisam representar essas formas separadas usando classes de associação de modelo separadas ou atributos separados [BIND (include = "â € ¦")] nos métodos de ação.</li><li>**O que são modelos de associação? Eles são a mesma coisa que os modelos de exibição? -** Esses são dois conceitos relacionados. O termo modelo de associação refere-se a uma classe de modelo usada na lista de parâmetros de uma ação (a forma passada da Associação de modelo do MVC para o método de ação). O modelo de exibição de termo refere-se a uma classe de modelo passada de um método de ação para um modo de exibição. Usar um modelo específico de exibição é uma abordagem comum para passar dados de um método de ação para um modo de exibição. Geralmente, essa forma também é adequada para a associação de modelo e o modelo de exibição de termo pode ser usado para fazer referência ao mesmo modelo usado em ambos os locais. Para ser preciso, este procedimento aborda especificamente os modelos de associação, concentrando-se na forma passada para a ação, que é o que importa para fins de atribuição em massa.</li></ul>| 

## <a id="rendering"></a>Codificar saída da Web não confiável antes da renderização

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Web Forms, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Como impedir o script entre sites no ASP.net](https://msdn.microsoft.com/library/ms998274.aspx), [script entre sites](https://cwe.mitre.org/data/definitions/79.html), folha de consulta de [prevenção de XSS (cross site scripting)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Passos** | O script entre sites (geralmente abreviado como XSS) é um vetor de ataque para serviços online ou qualquer aplicativo/componente que consuma a entrada da Web. As vulnerabilidades de XSS podem permitir que um invasor execute o script na máquina de outro usuário por meio de um aplicativo Web vulnerável. Scripts mal-intencionados podem ser usados para roubar cookies e adulterar, de outra forma, a um computador da vítima por meio de JavaScript. O XSS é impedido pela validação da entrada do usuário, garantindo que ela seja bem formada e codificada antes de ser renderizada em uma página da Web. A validação de entrada e a codificação de saída podem ser feitas usando a biblioteca de proteção da Web. Para código gerenciado (C\#, VB.net, etc.), use um ou mais métodos de codificação apropriados da biblioteca de proteção da Web (anti-XSS), dependendo do contexto em que a entrada do usuário é manifestada:| 

### <a name="example"></a>Exemplo

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>Executar validação e filtragem de entrada em todas as propriedades de modelo de tipo de cadeia de caracteres

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Adicionando validação](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [Validando dados de modelo em um aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [princípios de orientação para seus aplicativos MVC ASP.net](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | <p>Todos os parâmetros de entrada devem ser validados antes de serem usados no aplicativo para garantir que o aplicativo seja protegido contra entradas de usuário mal-intencionadas. Valide os valores de entrada usando validações de expressão regular no lado do servidor com uma estratégia de validação de lista de permissões. Entradas/parâmetros de usuário não limpos passados para os métodos podem causar vulnerabilidades de injeção de código.</p><p>Para aplicativos Web, os pontos de entrada também podem incluir campos de formulário, QueryStrings, cookies, cabeçalhos HTTP e parâmetros de serviço Web.</p><p>As seguintes verificações de validação de entrada devem ser executadas na associação de modelo:</p><ul><li>As propriedades do modelo devem ser anotadas com a anotação RegularExpression, para aceitar os caracteres permitidos e o comprimento máximo permitido</li><li>Os métodos do controlador devem executar a validade ModelState</li></ul>|

## <a id="richtext"></a>A limpeza deve ser aplicada em campos de formulário que aceitam todos os caracteres, por exemplo, editor de Rich Text

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Codificar entrada não segura](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [limpador de HTML](https://github.com/mganss/HtmlSanitizer) |
| **Passos** | <p>Identifique todas as marcas de marcação estática que você deseja usar. Uma prática comum é restringir a formatação a elementos HTML seguros, como `<b>` (negrito) e `<i>` (itálico).</p><p>Antes de gravar os dados, codifique-o em HTML. Isso torna qualquer script mal-intencionado seguro, fazendo com que ele seja tratado como texto, não como código executável.</p><ol><li>Desabilitar a validação de solicitação de ASP.net pelo atributo adicionando o ValidateRequest = "false" \@ à diretiva Page</li><li>Codificar a entrada de cadeia de caracteres com o método HtmlEncode</li><li>Use um StringBuilder e chame seu método Replace para remover seletivamente a codificação nos elementos HTML que você deseja permitir</li></ol><p>A página-nas referências desabilita a validação da solicitação ASP.NET por meio da `ValidateRequest="false"`configuração. Ele codifica a entrada e, seletivamente, permite o `<b>` e `<i>` , como alternativa, uma biblioteca .net para a limpeza de HTML também pode ser usada.</p><p>HtmlSanitizer é uma biblioteca .NET para limpar fragmentos e documentos HTML de construções que podem levar a ataques de XSS. Ele usa AngleSharp para analisar, manipular e renderizar HTML e CSS. O HtmlSanitizer pode ser instalado como um pacote NuGet, e a entrada do usuário pode ser passada por meio de métodos de limpeza HTML ou CSS relevantes, conforme aplicável, no lado do servidor. Observe que a limpeza como um controle de segurança deve ser considerada apenas como última opção.</p><p>A validação de entrada e a codificação de saída são consideradas melhores controles de segurança.</p> |

## <a id="inbuilt-encode"></a>Não atribua elementos DOM a coletores que não têm codificação embutida

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Muitas funções JavaScript não fazem a codificação por padrão. Ao atribuir entrada não confiável a elementos DOM por meio dessas funções, o pode resultar em execuções de script entre sites (XSS).| 

### <a name="example"></a>Exemplo
A seguir estão exemplos inseguros: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Não use `innerHtml`; em vez `innerText`disso, use. Da mesma forma, `$("#elm").html()`em vez de, use`$("#elm").text()` 

## <a id="redirect-safe"></a>Validar que todos os redirecionamentos dentro do aplicativo sejam fechados ou concluídos com segurança

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [A estrutura de autorização do OAuth 2,0-redirecionadores abertos](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Passos** | <p>O design do aplicativo que requer redirecionamento para um local fornecido pelo usuário deve restringir os destinos de redirecionamento possíveis para uma lista predefinida "segura" de sites ou domínios. Todos os redirecionamentos no aplicativo devem ser fechados/seguros.</p><p>Para efetuar este procedimento:</p><ul><li>Identificar todos os redirecionamentos</li><li>Implemente uma atenuação apropriada para cada redirecionamento. As atenuações apropriadas incluem a lista de permissões de redirecionamento ou confirmação do usuário. Se um site ou serviço com uma vulnerabilidade de redirecionamento aberto usar provedores de identidade do Facebook/OAuth/OpenID, um invasor poderá roubar o token de logon de um usuário e representar esse usuário. Esse é um risco inerente ao usar o OAuth, que está documentado na RFC 6749 "a estrutura de autorização do OAuth 2,0", seção 10,15 "redirecionamentos abertos" da mesma forma, as credenciais dos usuários podem ser comprometidas por ataques de spear phishing usando redirecionamentos abertos</li></ul>|

## <a id="string-method"></a>Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos por métodos do controlador

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Validando dados de modelo em um aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [princípios de orientação para seus aplicativos MVC ASP.net](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Para métodos que aceitam apenas tipos de dados primitivos, e não modelos como argumento, a validação de entrada usando a expressão regular deve ser feita. Aqui, Regex. IsMatch deve ser usado com um padrão Regex válido. Se a entrada não corresponder à expressão regular especificada, o controle não deverá prosseguir mais e um aviso adequado sobre a falha de validação deverá ser exibido.| 

## <a id="dos-expression"></a>Definir o tempo limite de limite superior para o processamento de expressão regular para evitar DoS devido a expressões regulares inadequadas

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Web Forms, MVC5, MVC6  |
| **Atributos**              | N/A  |
| **Referências**              | [Propriedade DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Passos** | Para garantir ataques de negação de serviço contra expressões regulares criadas incorretamente, que causam muito retrocesso, defina o tempo limite padrão global. Se o tempo de Processamento demorar mais do que o limite superior definido, ele lançaria uma exceção de tempo limite. Se nada estiver configurado, o tempo limite seria infinito.| 

### <a name="example"></a>Exemplo
Por exemplo, a configuração a seguir gerará um RegexMatchTimeoutException, se o processamento levar mais de 5 segundos: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Evite usar HTML. RAW em exibições do Razor

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Passo | O Razor (ASP.NET Web Pages) executa codificação HTML automática. Todas as cadeias de caracteres impressas por nuggets de código inseridas (@ Blocks) são codificadas automaticamente em HTML. No entanto `HtmlHelper.Raw` , quando o método é invocado, ele retorna marcação que não é codificada em HTML. Se `Html.Raw()` o método auxiliar for usado, ele ignorará a proteção de codificação automática que o Razor fornece.|

### <a name="example"></a>Exemplo
Veja a seguir um exemplo inseguro: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Não use `Html.Raw()` a menos que você precise exibir a marcação. Esse método não executa a codificação de saída implicitamente. Use outros auxiliares do ASP.NET, por exemplo,`@Html.DisplayFor()` 

## <a id="stored-proc"></a>Não usar consultas dinâmicas em procedimentos armazenados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Um ataque de injeção de SQL explora vulnerabilidades na validação de entrada para executar comandos arbitrários no banco de dados. Ele pode ocorrer quando seu aplicativo usa a entrada para construir instruções SQL dinâmicas para acessar o banco de dados. Isso também pode ocorrer se o seu código usa procedimentos armazenados que são cadeias de caracteres passadas que contêm entrada de usuário bruto. Usando o ataque de injeção de SQL, o invasor pode executar comandos arbitrários no banco de dados. Todas as instruções SQL (incluindo as instruções SQL em procedimentos armazenados) devem ser parametrizadas. Instruções SQL parametrizadas aceitarão caracteres que têm significado especial para o SQL (como aspas simples) sem problemas porque são fortemente tipadas. |

### <a name="example"></a>Exemplo
Veja a seguir um exemplo de procedimento armazenado dinâmico não seguro: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Exemplo
A seguir está o mesmo procedimento armazenado implementado com segurança: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>Verifique se a validação do modelo é feita nos métodos da API Web

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Validação de modelo no ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Quando um cliente envia dados a uma API da Web, é obrigatório validar os dados antes de fazer qualquer processamento. Para APIs Web ASP.NET que aceitam modelos como entrada, use anotações de dados em modelos para definir regras de validação nas propriedades do modelo.|

### <a name="example"></a>Exemplo
O código a seguir demonstra o mesmo: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Exemplo
No método de ação dos controladores de API, a validade do modelo deve ser marcada explicitamente conforme mostrado abaixo: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos por métodos de API Web

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC 5, MVC 6 |
| **Atributos**              | N/A  |
| **Referências**              | [Validando dados de modelo em um aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [princípios de orientação para seus aplicativos MVC ASP.net](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Para métodos que aceitam apenas tipos de dados primitivos, e não modelos como argumento, a validação de entrada usando a expressão regular deve ser feita. Aqui, Regex. IsMatch deve ser usado com um padrão Regex válido. Se a entrada não corresponder à expressão regular especificada, o controle não deverá prosseguir mais e um aviso adequado sobre a falha de validação deverá ser exibido.|

## <a id="typesafe-api"></a>Verifique se os parâmetros de tipo seguro são usados na API Web para acesso a dados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Se você usar a coleção Parameters, o SQL tratará que a entrada é como um valor literal em vez de código executável. A coleção Parameters pode ser usada para impor restrições de tipo e comprimento em dados de entrada. Os valores fora do intervalo disparam uma exceção. Se os parâmetros SQL de tipo seguro não forem usados, os invasores poderão executar ataques de injeção inseridos na entrada não filtrada.</p><p>Use parâmetros de tipo seguro ao construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com entrada não filtrada. Você pode usar parâmetros de tipo seguro com procedimentos armazenados e instruções SQL dinâmicas. Os parâmetros são tratados como valores literais pelo banco de dados e não como código executável. Os parâmetros também são verificados quanto ao tipo e comprimento.</p>|

### <a name="example"></a>Exemplo
O código a seguir mostra como usar parâmetros de tipo seguro com o SqlParameterCollection ao chamar um procedimento armazenado. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
No exemplo de código anterior, o valor de entrada não pode ter mais de 11 caracteres. Se os dados não estiverem de acordo com o tipo ou o comprimento definido pelo parâmetro, a classe SqlParameter lançará uma exceção. 

## <a id="sql-docdb"></a>Usar consultas SQL parametrizadas para Cosmos DB

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | BD de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Anunciando a parametrização do SQL no Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Passos** | Embora Azure Cosmos DB só dê suporte a consultas somente leitura, a injeção de SQL ainda será possível se as consultas forem construídas por meio da concatenação com a entrada do usuário. Pode ser possível que um usuário tenha acesso aos dados que eles não devem acessar na mesma coleção, criando consultas SQL mal-intencionadas. Use consultas SQL parametrizadas se as consultas forem construídas com base na entrada do usuário. |

## <a id="schema-binding"></a>Validação de entrada do WCF por meio de associação de esquema

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Passos** | <p>A falta de validação leva a ataques de injeção de tipo diferentes.</p><p>A validação de mensagem representa uma linha de defesa na proteção do aplicativo WCF. Com essa abordagem, você valida mensagens usando esquemas para proteger as operações de serviço do WCF contra ataques por um cliente mal-intencionado. Valide todas as mensagens recebidas pelo cliente para proteger o cliente contra ataques por um serviço mal-intencionado. A validação de mensagem torna possível validar mensagens quando as operações consomem contratos de mensagem ou contratos de dados, o que não pode ser feito usando a validação de parâmetro. A validação de mensagem permite que você crie lógica de validação dentro de esquemas, fornecendo, assim, mais flexibilidade e reduzindo o tempo de desenvolvimento. Os esquemas podem ser reutilizados em diferentes aplicativos dentro da organização, criando padrões para a representação de dados. Além disso, a validação de mensagens permite que você proteja as operações quando elas consomem tipos de dados mais complexos envolvendo contratos que representam a lógica de negócios.</p><p>Para executar a validação de mensagens, primeiro crie um esquema que represente as operações do serviço e os tipos de dados consumidos por essas operações. Em seguida, você cria uma classe .NET que implementa um inspetor de mensagem de cliente personalizado e um inspetor de mensagem de Dispatcher personalizado para validar as mensagens enviadas/recebidas de/para o serviço. Em seguida, implemente um comportamento de ponto de extremidade personalizado para habilitar a validação de mensagens no cliente e no serviço. Por fim, você implementa um elemento de configuração personalizada na classe que permite expor o comportamento de ponto de extremidade personalizado estendido no arquivo de configuração do serviço ou do cliente "</p>|

## <a id="parameters"></a>WCF-validação de entrada por meio de inspetores de parâmetro

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Passos** | <p>A validação de dados e de entrada representa uma linha importante de defesa na proteção do aplicativo WCF. Você deve validar todos os parâmetros expostos nas operações de serviço do WCF para proteger o serviço contra ataques por um cliente mal-intencionado. Por outro lado, você também deve validar todos os valores de retorno recebidos pelo cliente para proteger o cliente contra ataques por um serviço mal-intencionado</p><p>O WCF fornece pontos de extensibilidade diferentes que permitem que você personalize o comportamento do tempo de execução do WCF criando extensões personalizadas. Os inspetores de mensagem e os inspetores de parâmetro são dois mecanismos de extensibilidade usados para obter maior controle sobre os dados que passam entre um cliente e um serviço. Você deve usar os inspetores de parâmetro para validação de entrada e usar os inspetores de mensagem somente quando precisar inspecionar a mensagem inteira que flui para dentro e para fora de um serviço.</p><p>Para executar a validação de entrada, você criará uma classe .NET e implementará um inspetor de parâmetro personalizado para validar parâmetros em operações em seu serviço. Em seguida, você implementará um comportamento de ponto de extremidade personalizado para habilitar a validação no cliente e no serviço. Por fim, você implementará um elemento de configuração personalizada na classe que permite expor o comportamento de ponto de extremidade personalizado estendido no arquivo de configuração do serviço ou do cliente</p>|
