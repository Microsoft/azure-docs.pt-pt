---
title: Entrada de validação - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
description: atenuações para ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 0803ade7613480621a0cd87f9944ee5f55bf432c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899490"
---
# <a name="security-frame-input-validation--mitigations"></a>Quadro de segurança: Validação de entrada | Atenuações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Desativar o XSLT scripts para todas as transformações com folhas de estilo não fidedigno](#disable-xslt)</li><li>[Certifique-se de que cada página que pode conter o conteúdo de utilizador controlável opta ativamente por detecção automática de MIME](#out-sniffing)</li><li>[Proteger ou desativar a resolução de entidades XML](#xml-resolution)</li><li>[Aplicações com o HTTP. sys efetuar verificação de canonização do URL](#app-verification)</li><li>[Certifique-se de que controles adequados estão em vigor quando abertos ao recebimento de arquivos de usuários](#controls-users)</li><li>[Certifique-se de que os parâmetros de tipo seguro são utilizados numa aplicação Web para acesso a dados](#typesafe)</li><li>[Utilizar classes de ligações de modelo separado ou apresenta uma lista de filtro de associação para impedir a vulnerabilidade de atribuição em massa do MVC](#binding-mvc)</li><li>[Codificar a saída da web não fidedignos antes da composição](#rendering)</li><li>[Realizar validação de entrada e filtragem no tipo de cadeia de caracteres de todas as propriedades do modelo](#typemodel)</li><li>[A limpeza deve ser aplicada em campos de formulário que aceitam todos os carateres, por exemplo, o editor de texto avançado](#richtext)</li><li>[Não atribua elementos DOM a Coletores de que não têm a codificação incorporadas](#inbuilt-encode)</li><li>[Validar se todos os redirecionamentos de dentro do aplicativo são fechados ou feitos com segurança](#redirect-safe)</li><li>[Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceites pelos métodos de controlador](#string-method)</li><li>[Definir o tempo limite de limite superior para a expressão regular de processamento para impedir que o DoS devido a más expressões regulares](#dos-expression)</li><li>[Evite utilizar Html.Raw em modos de exibição do Razor](#html-razor)</li></ul> | 
| **Base de Dados** | <ul><li>[Não utilize consultas dinâmicas em procedimentos armazenados](#stored-proc)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a validação de modelo é feita nos métodos da Web API](#validation-api)</li><li>[Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceites pelos métodos da Web API](#string-api)</li><li>[Certifique-se de que são utilizados os parâmetros de tipo seguro na Web API para acesso a dados](#typesafe-api)</li></ul> | 
| **DB de documentos do Azure** | <ul><li>[Utilizar consultas SQL parametrizadas para o Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Validação de entrada do WCF por meio da associação de esquema](#schema-binding)</li><li>[Validação de entrada WCF por meio dos Inspetores de parâmetro](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Desativar o XSLT scripts para todas as transformações com folhas de estilo não fidedigno

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Segurança XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript propriedade](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Passos** | XSLT oferece suporte a scripts no interior de folhas de estilo usando o `<msxml:script>` elemento. Isso permite que as funções personalizadas ser usado numa transformação XSLT. O script é executado sob o contexto do processo de executar a transformação. XSLT script tem de ser desativada quando num ambiente não fidedigno para impedir a execução de código não confiável. *Se utilizar o .NET:* Scripts de XSLT está desativada por padrão. No entanto, tem de garantir que ele não foi explicitamente ativado através do `XsltSettings.EnableScript` propriedade.|

### <a name="example"></a>Exemplo 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver a utilizar o MSXML 6.0, XSLT de scripts está desativada por padrão. No entanto, tem de se certificar de que ele não foi explicitamente ativado por meio da propriedade de objeto do XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver usando MSXML 5 ou abaixo, a criação de scripts do XSLT está ativada por predefinição e deve usar explicitamente desativá-la. Defina a propriedade de objeto do XML DOM AllowXsltScript como false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Certifique-se de que cada página que pode conter o conteúdo de utilizador controlável opta ativamente por detecção automática de MIME

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [IE8 V de parte de segurança-proteção abrangente](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Passos** | <p>Para cada página que pode conter conteúdo controlável do utilizador, tem de utilizar o cabeçalho de HTTP `X-Content-Type-Options:nosniff`. Para cumprir este requisito, pode optar por definir o cabeçalho necessário página por página para apenas as páginas que podem conter o conteúdo de utilizador controlável ou pode configurá-lo globalmente para todas as páginas no aplicativo.</p><p>Cada tipo de ficheiro fornecido de um servidor web tem um associados [tipo de MIME](https://en.wikipedia.org/wiki/Mime_type) (também chamado de um *tipo de conteúdo*) que descreve a natureza do conteúdo (ou seja, imagem, texto, aplicação, etc.)</p><p>O cabeçalho X-conteúdo-tipo-Options é um cabeçalho HTTP que permite que os desenvolvedores especifiquem que seu conteúdo não deve ser intercetados de MIME. Este cabeçalho é projetado para mitigar ataques de detecção de MIME. Foi adicionado suporte para esse cabeçalho no Internet Explorer 8 (IE8)</p><p>Apenas os usuários do Internet Explorer 8 (IE8) serão beneficiados pelo X-conteúdo-tipo-Options. As versões anteriores do Internet Explorer não respeitarem atualmente o cabeçalho X-conteúdo-tipo-Options</p><p>Internet Explorer 8 (e posterior) são os browsers principais apenas para implementar um recurso de sair de detecção de MIME. Se e quando principais navegadores (Firefox, Safari, Chrome) implementam recursos semelhantes, esta recomendação será atualizada para incluir a sintaxe para esses navegadores</p>|

### <a name="example"></a>Exemplo
Para ativar o cabeçalho necessário globalmente para todas as páginas do aplicativo, pode fazer um dos seguintes: 

* Adicione o cabeçalho no ficheiro Web. config, se o aplicativo é hospedado pelos serviços de informação Internet (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Adicionar cabeçalho através do aplicativo global\_BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Módulo HTTP personalizado de implementar 

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

* Pode ativar o cabeçalho necessário apenas para páginas específicas ao adicioná-la para as respostas individuais: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Proteger ou desativar a resolução de entidades XML

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Expansão da entidade XML](https://capec.mitre.org/data/definitions/197.html), [XML de negação de serviço de ataques e defesas](https://msdn.microsoft.com/magazine/ee335713.aspx), [descrição geral da segurança do MSXML](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [melhores práticas para proteger o código do MSXML](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Referência do protocolo de NSXMLParserDelegate](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [resolver as referências externas](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Passos**| <p>Embora não é amplamente utilizado, há um recurso do XML que permite que o analisador XML expandir as entidades de macro com valores definidos dentro do próprio documento ou a partir de origens externas. Por exemplo, o documento pode definir uma entidade "companyname" com o valor "Microsoft", então, que sempre que o texto "&companyname;" é apresentado no documento, é substituído automaticamente com o texto de Microsoft. Em alternativa, o documento pode definir uma entidade "MSFTStock" que faça referência a um serviço web externo para buscar o valor atual de estoque de Microsoft.</p><p>Em seguida, em qualquer altura "&MSFTStock;" é apresentado no documento, ele é automaticamente substituído pelo preço das ações atual. No entanto, essa funcionalidade pode ser abusada para criar a negação de condições de serviço (DoS). Um invasor pode aninhar várias entidades para criar uma bomba XML de expansão exponencial que consuma toda a memória disponível no sistema. </p><p>Em alternativa, ele pode criar uma referência externa que transmite os back uma quantidade infinita de dados ou que simplesmente pára o thread. Como resultado, todas as equipes terá de desativar a resolução de entidades XML interna e/ou externa inteiramente se seu aplicativo não usá-lo, ou manualmente limitar a quantidade de memória e a hora em que o aplicativo pode consumir para a resolução de entidade se esta funcionalidade está absolutamente necessário. Se a resolução de entidade não é exigida pela sua aplicação, em seguida, desativá-la. </p>|

### <a name="example"></a>Exemplo
Para o código do .NET Framework, pode usar as seguintes abordagens:

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
Tenha em atenção que o valor predefinido `ProhibitDtd` no `XmlReaderSettings` for VERDADEIRO, mas em `XmlTextReader` é falso. Se estiver a utilizar XmlReaderSettings, não é necessário definir ProhibitDtd como verdadeiro explicitamente, mas é recomendado para o efeito de segurança que o faça. Observe também que a classe XmlDocument permite a resolução de entidade por predefinição. 

### <a name="example"></a>Exemplo
Para desativar a resolução de entidades para XmlDocument, utilize o `XmlDocument.Load(XmlReader)` de sobrecarga do método de carga e definir as propriedades adequadas no argumento XmlReader para desativar a resolução, conforme ilustrado no seguinte código: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exemplo
Se desativar a resolução de entidade não é possível para a sua aplicação, defina a propriedade de XmlReaderSettings.MaxCharactersFromEntities como um valor razoável, de acordo com as necessidades da sua aplicação. Isso limitará o impacto de potenciais ataques de expansão exponencial. O código a seguir fornece um exemplo desta abordagem: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exemplo
Se tiver de resolver entidades inline, mas fazer não é necessário resolver entidades externas, defina a propriedade de XmlReaderSettings.XmlResolver como nulo. Por exemplo: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Tenha em atenção que o MSXML6, ProhibitDTD está definido como true (processamento de DTD desativar) por predefinição. Para o código de Apple OSX/iOS, existem dois analisadores XML, que pode usar: NSXMLParser e libXML2. 

## <a id="app-verification"></a>Aplicações com o HTTP. sys efetuar verificação de canonização do URL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Qualquer aplicação que utiliza HTTP. sys deve seguir estas diretrizes:</p><ul><li>Limite o comprimento da URL para não mais do que 16.384 carateres (ASCII ou Unicode). Este é o comprimento de URL de máximo absoluto, com base na predefinição de serviços de informação Internet (IIS) 6. Web sites devem se esforçar durante um período menor do que isso se possível</li><li>Utilizar as classes de e/s de arquivo padrão do .NET Framework (como FileStream) como estes serão aproveitar as regras de canonização no .NET FX</li><li>Criar explicitamente uma lista de permissões de nomes de ficheiros conhecidos</li><li>Rejeitar explicitamente conhecidos tipos de ficheiro não irá servir rejeita do UrlScan: exe, morcego, cmd, com, htw, ida, idq, htr, idc, shtm [l], stm, impressora, ini, pol, arquivos de dados</li><li>Captura as seguintes exceções:<ul><li>System.ArgumentException (para os nomes de dispositivo)</li><li>System.NotSupportedException (para fluxos de dados)</li><li>FileNotFoundException (para nomes de ficheiros com caráter de escape inválido)</li><li>System.IO.DirectoryNotFoundException (para dirs com caráter de escape inválido)</li></ul></li><li>*Isso não é possível* chamar APIs de e/s de arquivos do Win32. Num URL inválido com elegância retornar um erro 400 para o usuário e registrar o erro real.</li></ul>|

## <a id="controls-users"></a>Certifique-se de que controles adequados estão em vigor quando abertos ao recebimento de arquivos de usuários

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Sem restrições de carregamento de ficheiros](https://www.owasp.org/index.php/Unrestricted_File_Upload), [tabela de assinatura de ficheiros](https://www.garykessler.net/library/file_sigs.html) |
| **Passos** | <p>Os ficheiros carregados representam um risco significativo para aplicações.</p><p>O primeiro passo para muitos ataques é obter alguns códigos para o sistema para ser atacado. Em seguida, o ataque só precisa de encontrar uma forma de obter o código executado. Usar o carregamento do ficheiro ajuda o invasor conseguir a primeira etapa. As conseqüências de carregamento de ficheiros sem restrições podem variar, incluindo obtenção de controlo completo do sistema, um sistema de ficheiros sobrecarregado ou a base de dados, ataques de sistemas back-end e desfiguração simple de reencaminhamento.</p><p>Depende de que o aplicativo faz com que o ficheiro carregado e especialmente onde estão armazenados. Validação do lado do servidor de carregamentos de ficheiros está em falta. Controlos de segurança a seguir deve ser implementada para a funcionalidade de carregamento de ficheiros:</p><ul><li>Verificação de extensão de ficheiro (deve ser aceite apenas um conjunto válido de tipo de ficheiro permitidos)</li><li>Limite de tamanho máximo do ficheiro</li><li>Ficheiro não deve ser carregado para webroot; a localização deve ser um diretório na unidade de sistema não</li><li>Convenção de nomenclatura deverão ser seguidas, substitui, de modo a que o nome de ficheiro carregado tem um pouco da aleatoriedade, de modo a impedir que o ficheiro</li><li>Arquivos devem ser analisados relativamente a software antivírus antes de gravar no disco</li><li>Certifique-se de que o nome de ficheiro e quaisquer outros metadados (por exemplo, o caminho de ficheiro) sejam validados para caracteres maliciosos</li><li>Assinatura de formato de ficheiro deve ser verificada, para impedir um utilizador de carregamento de um ficheiro masqueraded (por exemplo, carregar um arquivo exe, alterando a extensão para txt)</li></ul>| 

### <a name="example"></a>Exemplo
Para o último ponto sobre validação de assinatura de formato de ficheiro, consulte a classe abaixo para obter mais detalhes: 

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

## <a id="typesafe"></a>Certifique-se de que os parâmetros de tipo seguro são utilizados numa aplicação Web para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Se utilizar a coleção de parâmetros, o SQL trata a entrada é como um valor literal em vez disso, em seguida, como o código executável. A coleção de parâmetros pode ser usada para impor restrições de tipo e comprimento em dados de entrada. Valores fora do intervalo disparar uma exceção. Se não forem utilizados parâmetros SQL de tipo seguro, os atacantes poderão executar ataques de injeção incorporados na entrada não filtrada.</p><p>Utilize parâmetros de tipo seguro ao construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com a entrada não filtrada. Pode usar parâmetros de tipo seguro com procedimentos armazenados e com instruções SQL dinâmicas. Parâmetros são tratados como valores literais pela base de dados e não como código executável. Parâmetros também são verificados para o tipo e comprimento.</p>|

### <a name="example"></a>Exemplo 
O código seguinte mostra como utilizar parâmetros de tipo seguro com o SqlParameterCollection ao chamar um procedimento armazenado. 

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
No exemplo de código anterior, o valor de entrada não pode ser mais de 11 carateres. Se os dados não está em conformidade com o tipo ou definido pelo parâmetro de comprimento, a classe SqlParameter lançará uma exceção. 

## <a id="binding-mvc"></a>Utilizar classes de ligações de modelo separado ou apresenta uma lista de filtro de associação para impedir a vulnerabilidade de atribuição em massa do MVC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Atributos de metadados](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [pública chave vulnerabilidade e atenuação de segurança](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [guia completo para atribuição de massa no ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [introdução ao EF usando a MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Passos** | <ul><li>**Quando devo procurar para publicação excessiva vulnerabilidades? -** Vulnerabilidades de publicação excessiva pode ocorrer a qualquer lugar vincular as classes de modelo de entrada do usuário. Estruturas como o MVC podem representar os dados de usuário em classes personalizadas do .NET, incluindo Plain Old CLR Objects (POCOs). MVC povoa automaticamente essas classes de modelo com dados de solicitação, fornecendo uma representação conveniente para lidar com entrada do usuário. Quando essas classes incluem as propriedades que não devem ser definidas pelo utilizador, a aplicação pode estar vulnerável a ataques, que permitem o controle de usuário de dados que a aplicação nunca se destina de publicação excessiva. Como a associação de modelo do MVC, base de dados do acesso a tecnologias como mapeadores relacional/objeto, como o Entity Framework, muitas vezes, também suporte usando objetos POCO para representar os dados de base de dados. Essas classes de modelo de dados fornecem a mesma conveniência no lidando com dados de base de dados, como MVC faz em lidar com entrada do usuário. Porque o MVC e a base de dados suportam modelos semelhantes, como objetos POCO, parece fácil reutilizar as mesmas classes para ambos os efeitos. Esta prática falha preservar a separação de preocupações e é uma área comuns em que as propriedades de não-intencionais são expostas a associação de modelo, permitindo que os ataques de lançamento excessiva.</li><li>**Por que não deve utilizar minhas classes de modelo de base de dados não filtrado como parâmetros para meu ações MVC? -** Associação de modelo MVC porque irá vincular a nada nessa classe. Mesmo que os dados não aparecerem na vista, um utilizador mal intencionado pode enviar um pedido HTTP com estes dados incluídos e MVC será disparará vinculá-lo uma vez que a ação diz que a classe de base de dados é a forma de dados, que ele deve aceitar entrada do usuário.</li><li>**Por que devo me preocupar sobre a forma utilizada para a associação de modelo? -** Associação de modelo usando o ASP.NET MVC com modelos de demasiado abrangentes expõe um aplicativo para ataques de publicação excessiva. Excesso de lançamento foi possível ativar a atacantes alterar os dados de aplicação para além de que o programador pretendido, por exemplo, substituindo o preço de um item ou os privilégios de segurança para uma conta. Os aplicativos devem usar o contrato de modelos (ou listas de filtros de propriedade permitidos específica) para fornecer um explícita de ligação de ação específica para as entradas não confiáveis permitir por meio de associação de modelo.</li><li>**É fazer com que os modelos de enlace separado apenas duplicação de código? -** Não é uma questão de separação de preocupações. Se reutilize modelos de base de dados em métodos de ação, está dizendo qualquer propriedade (ou subpropriedade) em que a classe pode ser definida pelo usuário numa solicitação HTTP. Se for esse não o que deseja MVC para o fazer, precisa de uma lista de filtro ou uma forma de classe separada para mostrar o MVC os dados que podem ser provenientes de em vez disso, a intervenção do utilizador.</li><li>**Se eu tiver modelos de enlace separado para a entrada do usuário, é necessário duplicar todos os atributos de anotação meus dados? -** Não necessariamente. Pode usar MetadataTypeAttribute na classe de modelo de base de dados para ligar a metadados numa classe de associação de modelo. Apenas observe que o tipo referenciado pelo MetadataTypeAttribute tem de ser um subconjunto do tipo de referência (ele pode ter menos propriedades, mas não mais).</li><li>**Mover dados e volta entre modelos de entrada do usuário e modelos de base de dados é entediante. Pode simplesmente copiar ao longo de todas as propriedades usando a reflexão? -** Sim. As únicas propriedades que aparecem nos modelos do enlace são os que determinou a ser seguros para a entrada do usuário. Não há nenhum motivo de segurança que impede a usando a reflexão para copiar ao longo de todas as propriedades que existem em comum entre esses dois modelos.</li><li>**Que tal [vincular (excluir = "â €¦")]. Posso utilizar que em vez de ter modelos de enlace separado? -** Essa abordagem não é recomendada. Com [vincular (excluir = "â €¦")] significa que qualquer nova propriedade é vinculáveis por predefinição. Quando é adicionada uma nova propriedade, existe um passo extra lembrar-se de manter as coisas segura, em vez de ter o design ser seguro por padrão. Dependendo do desenvolvedor verificar esta lista, sempre que for adicionada uma propriedade é arriscado.</li><li>**É [vincular (incluir = "â €¦")] útil para operações de edição? -** Não. [Ligar (incluir = "â €¦")] só é adequado para operações de inserção-estilo (adicionar dados novos). Para operações de ATUALIZAÇÃO-estilo (revisando os dados existentes), utilize outra abordagem, como ter modelos de enlace separado ou passar uma lista explícita de propriedades permitidas para UpdateModel ou TryUpdateModel. Adicionar um [vincular (incluir = "â €¦")] atributo numa operação de edição significa que o MVC irá criar uma instância de objeto e definir as propriedades listadas, deixando todos os outros com os valores padrão. Quando os dados são persistentes, substituirá totalmente a entidade existente, repor os valores para todas as propriedades omitidos para as predefinições. Por exemplo, se IsAdmin foi omitida de um [vincular (incluir = "â €¦")] atributo numa operação de edição, qualquer usuário cujo nome foi editado por esta ação teria de ser reposto para IsAdmin = false (qualquer utilizador editado perderia estado de administrador). Se quiser impedir que as atualizações para determinadas propriedades, utilize uma das outras abordagens acima. Tenha em atenção que algumas versões das ferramentas do MVC geram classes de controlador com [vincular (incluir = "â €¦")] sobre como editar ações e implica que uma propriedade a remover da lista irá impedir que os ataques de lançamento excessiva. No entanto, conforme descrito acima, essa abordagem não funciona como pretendido e em vez disso, irá repor todos os dados nas propriedades de omitido para os valores predefinidos.</li><li>**Para operações Create, existem os avisos com [vincular (incluir = "â €¦")] em vez de modelos de enlace separado? -** Sim. Primeiro essa abordagem não funciona para cenários de edição, que exigem a manutenção de duas abordagens separadas para a atenuação todas as vulnerabilidades de lançamento excessiva. Modelos de enlace separado, segundo impõem a separação das preocupações entre forma utilizada para a entrada do usuário e a forma utilizada para a persistência, algo [vincular (incluir = "â €¦")] não faz. Em terceiro lugar, tenha em atenção que [vincular (incluir = "â €¦")] pode apenas tratar de propriedades de nível superior; Não é possível permitir apenas partes de subpropriedades (por exemplo, "Details.Name") no atributo. Por fim e talvez mais importante é que, usando [vincular (incluir = "â €¦")] Adiciona um passo extra que deve ser memorizado sempre que a classe é utilizado para a associação de modelo. Se um novo método de ação liga diretamente para a classe de dados e esquecer de incluir um [vincular (incluir = "â €¦")] atributo, ele pode ser vulnerável a ataques, de publicação excessiva pelo que a [vincular (incluir = "â €¦")] abordagem é um pouco menos segura por padrão. Se usar [vincular (incluir = "â €¦")], tenha cuidado sempre se lembrar de especificá-lo sempre que suas classes de dados são apresentados como parâmetros de método de ação.</li><li>**Para operações Create, que tal colocando o [vincular (incluir = "â €¦")] atributo na classe de modelo em si? Não, essa abordagem evitar a necessidade de não se esqueça de colocar o atributo em cada método de ação? -** Essa abordagem funciona em alguns casos. Com [vincular (incluir = "â €¦")] o próprio tipo de modelo (em vez de parâmetros de ação usando essa classe), a evitar a necessidade de não se esqueça de incluir o [vincular (incluir = "â €¦")] atributo em cada método de ação. Usando o atributo diretamente à classe de forma eficaz, cria uma área de superfície separada dessa classe para fins de vinculação de modelo. No entanto, esta abordagem permite apenas para uma forma de associação de modelo por classe de modelo. Se um método de ação tem de permitir que a associação de modelo de um campo (por exemplo, um administrador só ação que Atualize funções de utilizador) e outras ações tem de impedir a associação de modelo deste campo, essa abordagem não funcionará. Cada classe só pode ter uma forma de associação de modelo; Se precisam de ações diferentes formas de associação de modelo diferente, que precisam para representar essas formas separadas usando qualquer uma das classes de ligações de modelo separado ou separar [vincular (incluir = "â €¦")] atributos sobre os métodos de ação.</li><li>**O que uma ligação modelos? Eles são a mesma coisa que modelos de exibição? -** São dois conceitos relacionados. O modelo de ligação do termo refere-se para uma classe de modelo utilizada na lista de parâmetros de uma ação (a forma passada de associação de modelo MVC para o método de ação). O modelo de exibição do termo refere-se para uma classe de modelo transmitida de um método de ação para uma vista. Usando um modelo de específica de exibição é uma abordagem comum para passar dados de um método de ação para uma vista. Muitas vezes, esta forma também é adequada para a associação de modelo e o modelo de exibição do termo pode ser usado para consultar o mesmo modelo usado em ambos os locais. Para ser preciso, este procedimento fale especificamente sobre o enlace de modelos, concentrando-se na forma passada para a ação, o que é o que é importante para fins de atribuição em massa.</li></ul>| 

## <a id="rendering"></a>Codificar a saída da web não fidedignos antes da composição

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genéricas, Web Forms, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Como impedir scripts entre sites no ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [Scripting entre sites](https://cwe.mitre.org/data/definitions/79.html), [folha de referência de prevenção de XSS (Cross scripts sites)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Passos** | Scripts entre sites (frequentemente abreviado como XSS) é um vetor de ataque de serviços online ou qualquer aplicação/componente que consome a entrada da web. As vulnerabilidades de XSS podem permitir que um invasor execute o script na máquina de outro utilizador através de um aplicativo web vulnerável. Scripts maliciosos podem ser utilizados para roubar cookies e caso contrário, adulterar a máquina de uma vítima através do JavaScript. XSS é impedida pela validação de entrada do utilizador, garantir que ela estiver bem formada e codificação antes de ele é processado numa página da web. Validação de entrada e a codificação de saída podem ser feitos usando Web Protection Library. Para código gerido (C\#, VB.NET, etc.), utilize um ou mais apropriado de métodos de codificação da biblioteca de proteção da Web (Anti-XSS), dependendo do contexto em que a entrada do usuário é apresentada:| 

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

## <a id="typemodel"></a>Realizar validação de entrada e filtragem no tipo de cadeia de caracteres de todas as propriedades do modelo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Adicionar validação](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [validar os dados de modelo num aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [princípios básicos para aplicativos ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | <p>Todos os parâmetros de entrada têm de ser validados antes de serem utilizadas no aplicativo para se certificar de que a aplicação é protegida contra entradas de utilizador mal intencionado. Valide os valores de entrada usando validações de expressão regular no lado do servidor com uma estratégia de validação da lista de permissões. Unsanitized entradas do utilizador / parâmetros passados para os métodos podem fazer com que o código vulnerabilidades de injeção.</p><p>Para aplicações web, pontos de entrada também podem incluir campos de formulário, QueryStrings, cookies, cabeçalhos HTTP e parâmetros do serviço web.</p><p>As seguintes verificações de validação de entrada tem de ser executadas após a associação de modelo:</p><ul><li>As propriedades do modelo devem ser anotadas com anotação RegularExpression, para aceitar caracteres permitidos e o comprimento máximo permitido</li><li>Os métodos de controlador devem executar ModelState validade</li></ul>|

## <a id="richtext"></a>A limpeza deve ser aplicada em campos de formulário que aceitam todos os carateres, por exemplo, o editor de texto avançado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Uma entrada de codificar](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML Sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Passos** | <p>Identifique todas as marcas de marcação estática que pretende utilizar. Uma prática comum é restringir a formatação para elementos HTML seguros, tais como `<b>` (negrito) e `<i>` (itálico).</p><p>Antes de escrever os dados, codificar com HTML-lo. Isso faz com que qualquer script mal-intencionado seguro, causando-lo ser tratada como texto, não como código executável.</p><ol><li>Desativar a validação de pedido do ASP.NET adicionando o o ValidateRequest = "false" atributo para o \@ diretiva Page</li><li>Codificar a entrada de cadeia de caracteres com o método HtmlEncode</li><li>Utilizar um StringBuilder e chamar seu método de substituição para remover seletivamente a codificação nos elementos HTML que deseja permitir</li></ol><p>Página da validação do pedido ASP.NET desativa referências ao definir `ValidateRequest="false"`. Ele codifica HTML de entrada e seletivamente permite que o `<b>` e `<i>` em alternativa, uma biblioteca .NET para a limpeza de HTML também pode ser utilizada.</p><p>HtmlSanitizer é uma biblioteca de .NET para a limpeza de HTML fragmentos e documentos de construções que podem conduzir a ataques XSS. Ele usa o AngleSharp para analisar, manipular e compor HTML e CSS. HtmlSanitizer pode ser instalado como um pacote NuGet e a entrada do usuário pode ser passada por meio de HTML ou CSS a limpeza métodos relevantes, conforme aplicável, no lado do servidor. Tenha em atenção que a limpeza de como um controle de segurança deve ser considerada apenas como uma última opção.</p><p>Validação de entrada e a codificação de saída são consideradas a melhor controlos de segurança.</p> |

## <a id="inbuilt-encode"></a>Não atribua elementos DOM a Coletores de que não têm a codificação incorporadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Muitas funções de javascript não fazem a codificação por predefinição. Ao atribuir entradas não confiáveis a elementos DOM através de funções, pode resultar em cruzada execuções de script (XSS) do site.| 

### <a name="example"></a>Exemplo
Seguem-se exemplos de não seguro: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Não utilize `innerHtml`; em vez disso, utilize `innerText`. Da mesma forma, em vez de `$("#elm").html()`, utilize `$("#elm").text()` 

## <a id="redirect-safe"></a>Validar se todos os redirecionamentos de dentro do aplicativo são fechados ou feitos com segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [A estrutura de autorização de OAuth 2.0 - Redirecionadores abertos](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Passos** | <p>Design do aplicativo que requerem redirecionamento para uma localização fornecida pelo usuário tem de restringir os destinos de redirecionamento possíveis para uma lista predefinida de "segura" de sites ou domínios. Todos os redirecionamentos no aplicativo tem de ser fechado/seguro.</p><p>Para efetuar este procedimento:</p><ul><li>Identificar todos os redirecionamentos</li><li>Implemente um apropriado de atenuação para cada tipo de redirecionamento. Atenuações apropriadas incluem a confirmação de lista de permissões ou utilizador de redirecionamento. Se a um site ou serviço com uma vulnerabilidade de redirecionamento aberto utiliza fornecedores de identidade do Facebook/OAuth/OpenID, um invasor pode roubar o token de início de sessão de um utilizador e representar esse utilizador. Este é um risco inerente ao utilizar o OAuth, que está documentado em RFC 6749 "O OAuth 2.0 autorização Framework", secção 10.15 "abrir redireciona" da mesma forma, credenciais dos utilizadores podem ser comprometidas por ataques de phishing spear usando redirecionamentos abertos</li></ul>|

## <a id="string-method"></a>Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceites pelos métodos de controlador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [A validar o modelo de dados num aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [princípios básicos para aplicativos ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Para métodos que aceitam apenas o tipo de dados primitivos e não modelos como argumento, deve ser feita usando a expressão Regular de validação de entrada. Aqui, RegEx deve ser utilizada com um padrão regex válido. Se a entrada não corresponde à expressão Regular especificada, controle não deve avançar e deve ser apresentado um aviso adequado em relação à falha de validação.| 

## <a id="dos-expression"></a>Definir o tempo limite de limite superior para a expressão regular de processamento para impedir que o DoS devido a más expressões regulares

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genéricas, Web Forms, MVC5, MVC6  |
| **Atributos**              | N/A  |
| **Referências**              | [Propriedade de DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Passos** | Para garantir que ataques denial of service contra incorretamente o criado expressões regulares, o que fazer com que muitos backtracking, definir o tempo limite padrão global. Se o tempo de processamento demorar mais tempo do que o limite superior definido, ela lançaria uma exceção de tempo limite. Se nada estiver configurado, o tempo limite seria infinito.| 

### <a name="example"></a>Exemplo
Por exemplo, a seguinte configuração irá gerar um RegexMatchTimeoutException, se o processamento demorar mais de 5 segundos: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Evite utilizar Html.Raw em modos de exibição do Razor

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Passo | Páginas da Web ASP.NET (Razor) realizar a codificação de HTML automática. Todas as cadeias de caracteres impressas por preciosidades do código incorporado (@ blocos) são automaticamente codificado em HTML. No entanto, quando `HtmlHelper.Raw` método é invocado, ele retorna marcação que não é codificado em HTML. Se `Html.Raw()` é utilizado o método auxiliar, ele ignora a proteção automática de codificação que fornece o Razor.|

### <a name="example"></a>Exemplo
Segue-se um exemplo inseguro: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Não utilize `Html.Raw()` , a menos que precisa exibir a marcação. Este método não executa implicitamente a codificação de saída. Por exemplo, a utilizar outros auxiliares ASP.NET `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Não utilize consultas dinâmicas em procedimentos armazenados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Um ataque de injeção de SQL explora vulnerabilidades na validação de entrada para executar comandos arbitrários na base de dados. Pode ocorrer quando o aplicativo usa a entrada para construir instruções SQL dinâmicas para acessar o banco de dados. Também pode ocorrer se o seu código utiliza os procedimentos armazenados que são transmitidos a cadeias que contêm a entrada do usuário não processados. Utilizar o ataque de injeção de SQL, o invasor pode executar comandos arbitrários na base de dados. Todas as instruções de SQL (incluindo as instruções SQL em procedimentos armazenados) devem ser parametrizadas. Instruções de SQL parametrizadas irão aceitar caracteres que têm um significado especial para o SQL (como aspa) sem problemas, porque eles tem rigidez de tipos. |

### <a name="example"></a>Exemplo
Segue-se um exemplo de procedimento armazenado de dinâmica inseguro: 

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
Segue-se o mesmo procedimento armazenado implementado de forma segura: 
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

## <a id="validation-api"></a>Certifique-se de que a validação de modelo é feita nos métodos da Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/A  |
| **Referências**              | [Validação de modelo na ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Quando um cliente envia dados para uma API web, é obrigatório para validar os dados antes de efetuar qualquer processamento. Para APIs da Web ASP.NET que aceitar o modelos como entrada, utilize anotações de dados em modelos para definir regras de validação nas propriedades do modelo.|

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
O método de ação dos controladores da API, a validade do modelo tem a ser verificado explicitamente conforme mostrado abaixo: 

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

## <a id="string-api"></a>Implementar validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceites pelos métodos da Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC 5, 6 de MVC |
| **Atributos**              | N/A  |
| **Referências**              | [A validar o modelo de dados num aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [princípios básicos para aplicativos ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Para métodos que aceitam apenas o tipo de dados primitivos e não modelos como argumento, deve ser feita usando a expressão Regular de validação de entrada. Aqui, RegEx deve ser utilizada com um padrão regex válido. Se a entrada não corresponde à expressão Regular especificada, controle não deve avançar e deve ser apresentado um aviso adequado em relação à falha de validação.|

## <a id="typesafe-api"></a>Certifique-se de que são utilizados os parâmetros de tipo seguro na Web API para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Se utilizar a coleção de parâmetros, o SQL trata a entrada é como um valor literal em vez disso, em seguida, como o código executável. A coleção de parâmetros pode ser usada para impor restrições de tipo e comprimento em dados de entrada. Valores fora do intervalo disparar uma exceção. Se não forem utilizados parâmetros SQL de tipo seguro, os atacantes poderão executar ataques de injeção incorporados na entrada não filtrada.</p><p>Utilize parâmetros de tipo seguro ao construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com a entrada não filtrada. Pode usar parâmetros de tipo seguro com procedimentos armazenados e com instruções SQL dinâmicas. Parâmetros são tratados como valores literais pela base de dados e não como código executável. Parâmetros também são verificados para o tipo e comprimento.</p>|

### <a name="example"></a>Exemplo
O código seguinte mostra como utilizar parâmetros de tipo seguro com o SqlParameterCollection ao chamar um procedimento armazenado. 

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
No exemplo de código anterior, o valor de entrada não pode ser mais de 11 carateres. Se os dados não está em conformidade com o tipo ou definido pelo parâmetro de comprimento, a classe SqlParameter lançará uma exceção. 

## <a id="sql-docdb"></a>Utilizar consultas SQL parametrizadas para o Cosmos DB

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | DB de documentos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Anunciamos a parametrização de SQL no Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Passos** | Embora o Azure Cosmos DB suporta apenas consultas só de leitura, injeção de SQL ainda é possível se as consultas são construídas pela concatenação com entrada do usuário. Poderá ser possível que um utilizador obtenha acesso a dados que não devem ser acessando dentro da mesma coleção ao criar consultas SQL mal-intencionado. Utilize consultas SQL parametrizadas se consultas são construídas com base na entrada do usuário. |

## <a id="schema-binding"></a>Validação de entrada do WCF por meio da associação de esquema

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Passos** | <p>Falta de validação leva a ataques de injeção de tipo diferente.</p><p>Validação da mensagem representa uma linha de defesa na proteção da sua aplicação do WCF. Com esta abordagem, validar as mensagens usando esquemas para proteger a operações de serviço do WCF de ataque por um cliente malicioso. Valide todas as mensagens recebidas pelo cliente para proteger o cliente contra ataques por um serviço mal-intencionado. Validação da mensagem faz com que seja possível validar as mensagens quando operações de consumam os contratos de mensagem ou contratos de dados, que não podem ser feitos usando validação de parâmetro. Validação de mensagem permite-lhe criar a lógica de validação dentro de esquemas, assim, oferecendo mais flexibilidade e reduzindo o tempo de desenvolvimento. Esquemas podem ser reutilizados entre diferentes aplicativos dentro da organização, criando padrões para a representação de dados. Além disso, validação de mensagem permite-lhe proteger operações quando consumirem tipos de dados mais complexos que envolvem contratos que representa a lógica de negócios.</p><p>Para executar a validação de mensagem, primeiro crie um esquema que representa as operações do seu serviço e os tipos de dados consumidos por essas operações. Em seguida, cria uma classe do .NET que implementa um inspetor de mensagem de cliente personalizadas e o inspetor de mensagem do dispatcher personalizado para validar as mensagens enviado/recebido de/para o serviço. Em seguida, implementar um comportamento de ponto final personalizado para ativar a validação de mensagem com o cliente e o serviço. Por fim, implementar um elemento de configuração personalizada na classe que permite expor o comportamento de ponto final personalizado expandida no ficheiro de configuração de serviço ou o cliente"</p>|

## <a id="parameters"></a>Validação de entrada WCF por meio dos Inspetores de parâmetro

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | N/A  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Passos** | <p>Validação de entrada e de dados representa uma importante linha de defesa na proteção da sua aplicação do WCF. Deve validar todos os parâmetros expostos em operações de serviço do WCF para proteger o serviço de ataque por um cliente malicioso. Por outro lado, também deve validar todos os valores de retorno recebidos pelo cliente para proteger o cliente contra ataques por um serviço mal-intencionado</p><p>O WCF fornece pontos de extensibilidade diferentes que permitem-lhe personalizar o comportamento de tempo de execução do WCF através da criação de extensões personalizadas. Inspetores de mensagem e dos Inspetores de parâmetro são dois mecanismos de extensibilidade usados para obter maior controle sobre os passagem entre um cliente e um serviço de dados. Deve usar inspetores de parâmetro para validação de entrada e usar inspetores de mensagem apenas quando precisar de inspecionar a mensagem inteira que entram e saem de um serviço.</p><p>Para efetuar a validação de entrada, irá criar uma classe do .NET e implementar um inspetor de parâmetro personalizado para validar parâmetros nas operações no seu serviço. Em seguida, irá implementar um comportamento de ponto final personalizado para ativar a validação no cliente e o serviço. Por fim, irá implementar um elemento de configuração personalizada na classe que permite expor o comportamento de ponto final personalizado expandida no ficheiro de configuração de serviço ou o cliente</p>|
