---
title: Validação de entrada - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
description: atenuações para ameaças expostas na Ferramenta de Modelação de Ameaças
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
ms.openlocfilehash: 712a0707826f97f29b015a2c5892f8d20577e41b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687884"
---
# <a name="security-frame-input-validation--mitigations"></a>Quadro de segurança: Validação de entrada / Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Desative a script XSLT para todas as transformações usando folhas de estilo não confiáveis](#disable-xslt)</li><li>[Certifique-se de que cada página que possa conter conteúdo controlável do utilizador opta por não cheirar mime automático](#out-sniffing)</li><li>[Endurecer ou desativar a Resolução de Entidades XML](#xml-resolution)</li><li>[Aplicações que utilizam http.sys realizam verificação de canonicalização url](#app-verification)</li><li>[Certifique-se de que existem controlos adequados ao aceitar ficheiros dos utilizadores](#controls-users)</li><li>[Certifique-se de que os parâmetros de segurança do tipo são utilizados na Aplicação Web para acesso a dados](#typesafe)</li><li>[Utilize classes de ligação de modelos separados ou listas de filtros de ligação para evitar a vulnerabilidade da atribuição em massa do MVC](#binding-mvc)</li><li>[Codificar saída web não fidedigna antes da renderização](#rendering)</li><li>[Execute validação de entrada e filtragem em todas as propriedades do modelo do tipo de corda](#typemodel)</li><li>[A sanitização deve ser aplicada em campos de formulárioque aceitam todos os caracteres, por exemplo, editor de texto rico](#richtext)</li><li>[Não atribuir elementos DOM a sumidouros que não tenham codificação incorporada](#inbuilt-encode)</li><li>[Validar todos os redirecionamentos dentro da aplicação são fechados ou feitos com segurança](#redirect-safe)</li><li>[Implementar validação de entrada em todos os parâmetros do tipo de cadeia aceites pelos métodos do Controlador](#string-method)</li><li>[Definir limite limite máximo para o processamento regular de expressão para evitar DoS devido a más expressões regulares](#dos-expression)</li><li>[Evite usar html.Raw em vistas de barbear](#html-razor)</li></ul> | 
| **Base de dados** | <ul><li>[Não utilize consultas dinâmicas em procedimentos armazenados](#stored-proc)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a validação do modelo é feita nos métodos Web API](#validation-api)</li><li>[Implementar validação de entrada em todos os parâmetros do tipo de cadeia aceites pelos métodos Web API](#string-api)</li><li>[Certifique-se de que os parâmetros de segurança do tipo são utilizados na Web API para acesso a dados](#typesafe-api)</li></ul> | 
| **Documento Azure DB** | <ul><li>[Use consultas SQL parametrizadas para O Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Validação de entrada do WCF através da ligação schema](#schema-binding)</li><li>[WCF- Validação de entrada através de Inspetores de Parâmetros](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Desative a script XSLT para todas as transformações usando folhas de estilo não confiáveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Segurança XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript Property](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Passos** | XSLT suporta scripting dentro de `<msxml:script>` folhas de estilo usando o elemento. Isto permite que as funções personalizadas sejam usadas numa transformação XSLT. O guião é executado no contexto do processo que realiza a transformação. O script XSLT deve ser desativado quando num ambiente não confiável para impedir a execução de código não confiável. *Se utilizar .NET:* A escrita XSLT é desativada por padrão; no entanto, deve garantir que não foi `XsltSettings.EnableScript` explicitamente ativado através da propriedade.|

### <a name="example"></a>Exemplo 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver a utilizar mSXML 6.0, a escrita XSLT é desativada por defeito; no entanto, deve certificar-se de que não foi ativado explicitamente através da propriedade de objetos XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver a utilizar mSXML 5 ou abaixo, a script xSLT está ativada por padrão e deve desativá-la explicitamente. Detete a propriedade do objeto XML DOM AllowXsltScript para falso. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Certifique-se de que cada página que possa conter conteúdo controlável do utilizador opta por não cheirar mime automático

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IE8 Security Part V - Proteção Integral](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Passos** | <p>Para cada página que possa conter conteúdo controlável pelo `X-Content-Type-Options:nosniff`utilizador, deve utilizar o Cabeçalho HTTP . Para cumprir este requisito, pode definir a página de cabeçalho por página necessária apenas para as páginas que possam conter conteúdo controlável pelo utilizador, ou pode defini-lo globalmente para todas as páginas da aplicação.</p><p>Cada tipo de ficheiro entregue a partir de um servidor web tem um [tipo MIME](https://en.wikipedia.org/wiki/Mime_type) associado (também chamado *de tipo de conteúdo)* que descreve a natureza do conteúdo (isto é, imagem, texto, aplicação, etc.)</p><p>O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que o seu conteúdo não deve ser farejado mime. Este cabeçalho foi concebido para mitigar os ataques mime-sniffing. O suporte para este cabeçalho foi adicionado no Internet Explorer 8 (IE8)</p><p>Apenas os utilizadores do Internet Explorer 8 (IE8) beneficiarão de Opções x-content-type-options. Versões anteriores do Internet Explorer não respeitam atualmente o cabeçalho X-Content-Type-Options</p><p>O Internet Explorer 8 (e mais tarde) são os únicos principais navegadores a implementar uma funcionalidade de opt-out que cheira a MIME. Se e quando outros grandes navegadores (Firefox, Safari, Chrome) implementarem funcionalidades semelhantes, esta recomendação será atualizada para incluir sintaxe para esses navegadores também</p>|

### <a name="example"></a>Exemplo
Para ativar o cabeçalho necessário globalmente para todas as páginas da aplicação, pode fazer uma das seguintes: 

* Adicione o cabeçalho no ficheiro web.config se a aplicação for hospedada pelos Serviços de Informação da Internet (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Adicione o cabeçalho\_através do Pedido de Início de Aplicação Global 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementar módulo HTTP personalizado 

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

* Só é possível ativar o cabeçalho necessário para páginas específicas, adicionando-o a respostas individuais: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Endurecer ou desativar a Resolução de Entidades XML

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Expansão da Entidade XML](https://capec.mitre.org/data/definitions/197.html), [XML Negação de Ataques e Defesas de Serviços,](https://msdn.microsoft.com/magazine/ee335713.aspx)Visão Geral de [Segurança MSXML](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [Melhores Práticas para Assegurar código MSXML,](https://msdn.microsoft.com/library/ms759188(VS.85).aspx) [NSXMLParserDelegaÇão Referência](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html)do Protocolo , Resolução de [Referências Externas](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Passos**| <p>Embora não seja amplamente utilizado, existe uma característica do XML que permite ao parser XML expandir entidades macro com valores definidos dentro do próprio documento ou de fontes externas. Por exemplo, o documento pode definir uma entidade "nome de empresa" com&companyname;o valor "Microsoft", de modo que cada vez que o texto " aparece no documento, é automaticamente substituído pelo texto Microsoft. Ou, o documento pode definir uma entidade "MSFTStock" que referencia um serviço web externo para obter o valor atual das ações da Microsoft.</p><p>Em seguida,&MSFTStock;a qualquer momento " aparece no documento, é automaticamente substituído pelo preço atual das ações. No entanto, esta funcionalidade pode ser abusada para criar condições de negação de serviço (DoS). Um intruso pode nidificar várias entidades para criar uma bomba XML de expansão exponencial que consome toda a memória disponível no sistema. </p><p>Em alternativa, ele pode criar uma referência externa que transmite uma quantidade infinita de dados ou que simplesmente pendura o fio. Como resultado, todas as equipas devem desativar totalmente a resolução da entidade XML interna e/ou externa se a sua aplicação não a utilizar, ou limitar manualmente a quantidade de memória e tempo que a aplicação pode consumir para resolução de entidades se esta funcionalidade for absolutamente necessária. Se a resolução da entidade não for exigida pela sua aplicação, desative-a. </p>|

### <a name="example"></a>Exemplo
Para o código-quadro .NET, pode utilizar as seguintes abordagens:

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
Note que o `ProhibitDtd` valor `XmlReaderSettings` padrão de `XmlTextReader` in é verdadeiro, mas nele é falso. Se estiver a utilizar o XmlReaderSettings, não precisa de configurar o ProhibitDtd de forma verdadeira mente, mas é recomendado por razões de segurança que o faça. Note também que a classe XmlDocument permite a resolução da entidade por defeito. 

### <a name="example"></a>Exemplo
Para desativar a resolução `XmlDocument.Load(XmlReader)` da entidade para a XmlDocuments, utilize a sobrecarga do método load e detete as propriedades apropriadas no argumento XmlReader para desativar a resolução, como ilustrado no seguinte código: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exemplo
Se a resolução de entidades incapacitantes não for possível para a sua aplicação, detete teda a propriedade XmlReaderSettings.MaxCharactersFromEntities para um valor razoável de acordo com as necessidades da sua aplicação. Isto limitará o impacto de potenciais ataques do DoS de expansão exponencial. O seguinte código fornece um exemplo desta abordagem: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exemplo
Se precisar de resolver entidades inline mas não precisar de resolver entidades externas, detete tea propriedade XmlReaderSettings.XmlResolver como nula. Por exemplo: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Note que em MSXML6, o ProhibitDTD é definido como verdadeiro (desativando o processamento dTD) por padrão. Para o código Apple OSX/iOS, existem dois parsers XML que pode utilizar: NSXMLParser e libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Aplicações que utilizam http.sys realizam verificação de canonicalização url

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Qualquer aplicação que utilize http.sys deve seguir estas orientações:</p><ul><li>Limite o comprimento do URL a não mais de 16.384 caracteres (ASCII ou Unicode). Este é o comprimento máximo absoluto de URL baseado na definição padrão de Serviços de Informação de Internet (IIS) 6. Os websites devem esforçar-se por um comprimento mais curto do que este, se possível</li><li>Utilize as classes padrão .NET Framework File I/O (como FileStream) uma vez que estas tirarão partido das regras de canocalização nas .NET FX</li><li>Construa explicitamente uma lista de nomes de ficheiros conhecidos</li><li>Rejeite explicitamente os filetipos conhecidos que não servirá os rejeitados urlScan: exe, morcego, cmd.com, htw, ida, idq, htr, idc, shtm[l], stm, impressora, ini, pol, dat files</li><li>Apanhe as seguintes exceções:<ul><li>System.ArgumentException (para nomes de dispositivos)</li><li>Sistema.NotSupportedException (para fluxos de dados)</li><li>System.IO.FileNotFoundException (para nomes de ficheiros inválidos)</li><li>System.IO.DirectoryNotFoundException (para dirs inválidos)</li></ul></li><li>*Não* ligue para o ficheiro Win32 I/O APIs. Num URL inválido, devolva graciosamente um erro de 400 ao utilizador e inicie o erro real.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Certifique-se de que existem controlos adequados ao aceitar ficheiros dos utilizadores

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | Upload de [ficheiro sem restrições,](https://www.owasp.org/index.php/Unrestricted_File_Upload) [tabela de assinatura de ficheiros](https://www.garykessler.net/library/file_sigs.html) |
| **Passos** | <p>Os ficheiros enviados representam um risco significativo para as aplicações.</p><p>O primeiro passo em muitos ataques é conseguir que algum código para o sistema seja atacado. Então o ataque só precisa de encontrar uma maneira de executar o código. A utilização de um upload de ficheiro ajuda o intruso a realizar o primeiro passo. As consequências do upload sem restrições de ficheiros podem variar, incluindo a aquisição completa do sistema, um sistema de ficheiros sobrecarregado ou base de dados, reencaminhamento de ataques para sistemas de back-end e simples desfiguração.</p><p>Depende do que a aplicação faz com o ficheiro carregado e especialmente onde está armazenado. A validação lateral do servidor dos uploads de ficheiros está em falta. Devem ser implementados os seguintes controlos de segurança para a funcionalidade de upload de ficheiros:</p><ul><li>Verificação de extensão de ficheiros (apenas um conjunto válido de tipo de ficheiro permitido deve ser aceite)</li><li>Limite máximo de tamanho de ficheiro</li><li>O ficheiro não deve ser enviado para a webroot; a localização deve ser um diretório em unidade não-sistema</li><li>A convenção de nomeação deve ser seguida, de modo a que o nome do ficheiro carregado tenha alguma aleatoriedade, de modo a evitar a sobreescrita dos ficheiros</li><li>Os ficheiros devem ser digitalizados para antivírus antes de escrever em disco</li><li>Certifique-se de que o nome do ficheiro e quaisquer outros metadados (por exemplo, caminho de ficheiro) são validados para caracteres maliciosos</li><li>A assinatura do formato de ficheiro deve ser verificada, para evitar que um utilizador carregue um ficheiro mascarado (por exemplo, carregando um ficheiro exe alterando a extensão para txt)</li></ul>| 

### <a name="example"></a>Exemplo
Para o último ponto relativo à validação da assinatura do formato de ficheiro, consulte a classe abaixo para obter mais detalhes: 

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

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Certifique-se de que os parâmetros de segurança do tipo são utilizados na Aplicação Web para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Se utilizar a coleção Parâmetros, a SQL trata a entrada como um valor literal, em vez de como código executável. A recolha de parâmetros pode ser utilizada para impor restrições de tipo e comprimento nos dados de entrada. Valores fora do alcance desencadeiam uma exceção. Se não forem utilizados parâmetros SQL seguros de tipo, os atacantes poderão ser capazes de executar ataques de injeção incorporados na entrada não filtrada.</p><p>Utilize parâmetros seguros do tipo ao construir consultas SQL para evitar possíveis ataques de injeção SQL que podem ocorrer com entrada não filtrada. Pode utilizar parâmetros de tipo seguro com procedimentos armazenados e com declarações SQL dinâmicas. Os parâmetros são tratados como valores literais pela base de dados e não como código executável. Os parâmetros também são verificados para o tipo e o comprimento.</p>|

### <a name="example"></a>Exemplo 
O código seguinte mostra como utilizar parâmetros seguros de tipo com a SqlParameterCollection ao chamar um procedimento armazenado. 

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
No exemplo do código anterior, o valor de entrada não pode ser superior a 11 caracteres. Se os dados não estiverem em conformidade com o tipo ou comprimento definido sao a uma exceção. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Utilize classes de ligação de modelos separados ou listas de filtros de ligação para evitar a vulnerabilidade da atribuição em massa do MVC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Atributos de Metadados,](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute)Vulnerabilidade e Mitigação de [Segurança De Chave Pública,](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)Guia Completo para Atribuição de Massas em ASP.NET [MVC,](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx) [Começar com EF usando MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Passos** | <ul><li>**Quando devo procurar vulnerabilidades de sobre-postagem?** Vulnerabilidades de sobrepublicação podem ocorrer em qualquer lugar que ligue as classes de modelos a partir da entrada do utilizador. Quadros como o MVC podem representar os dados dos utilizadores em classes personalizadas .NET, incluindo objetos CLR simples e antigos (POCOs). O MVC povoa automaticamente estas classes-modelo com dados do pedido, proporcionando uma representação conveniente para lidar com a entrada do utilizador. Quando estas classes incluem propriedades que não devem ser definidas pelo utilizador, a aplicação pode ser vulnerável a ataques de sobre-postagem, que permitem o controlo do utilizador de dados que a aplicação nunca pretendeu. Tal como a ligação do modelo MVC, tecnologias de acesso à base de dados, como mappers de objetos/relacionais como o Quadro de Entidades, muitas vezes também suportam a utilização de objetos POCO para representar dados de base de dados. Estas classes de modelos de dados proporcionam a mesma comodidade em lidar com dados de base de dados como o MVC faz ao lidar com a entrada do utilizador. Como tanto o MVC como a base de dados suportam modelos semelhantes, como os objetos POCO, parece fácil reutilizar as mesmas classes para ambos os fins. Esta prática não preserva a separação de preocupações, e é uma área comum onde propriedades não intencionais são expostas a ligação de modelos, permitindo ataques de sobre-publicação.</li><li>Porque não devia usar as minhas aulas de modelos de base de **dados não filtradas como parâmetros para as minhas ações de MVC?** Porque a ligação do modelo MVC ligará qualquer coisa nessa classe. Mesmo que os dados não apareçam na sua opinião, um utilizador malicioso pode enviar um pedido HTTP com estes dados incluídos, e o MVC irá ligá-lo de bom grado porque a sua ação diz que a classe base de dados é a forma de dados que deve aceitar para a entrada do utilizador.</li><li>**Por que me importaria com a forma usada para a ligação de modelos?** A utilização ASP.NET modelo MVC com modelos demasiado amplos expõe uma aplicação a ataques de sobre-postagem. A sobrepublicação poderia permitir que os atacantes alterassem os dados da aplicação para além do pretendido pelo programador, tais como a sobreposição do preço de um item ou os privilégios de segurança de uma conta. As aplicações devem utilizar modelos de ligação específicos para a ação (ou listas específicas de filtros de propriedade) para fornecer um contrato explícito para o que a entrada não confiável permitir através da ligação do modelo.</li><li>**Ter modelos de ligação separados é apenas duplicar código?** Não, é uma questão de separação de preocupações. Se reutilizar modelos de base de dados em métodos de ação, está a dizer que qualquer propriedade (ou sub-propriedade) nessa classe pode ser definida pelo utilizador num pedido HTTP. Se não é isso que pretende que o MVC faça, precisa de uma lista de filtros ou de uma forma de classe separada para mostrar ao MVC quais os dados que podem vir da entrada do utilizador.</li><li>**Se eu tiver modelos de ligação separados para a entrada do utilizador, tenho de duplicar todos os meus atributos de anotação** de dados? Não necessariamente. Pode utilizar MetadadosTypeAttribute na classe modelo de base de dados para ligar aos metadados numa classe de ligação de modelo. Basta notar que o tipo referenciado pelo MetadataTypeAttribute deve ser um subconjunto do tipo de referenciação (pode ter menos propriedades, mas não mais).</li><li>Mover dados para trás e para a frente entre os modelos de entrada do utilizador e os modelos de base de **dados é aborrecido. Posso copiar todas as propriedades usando reflexo? Sim,** é um pouco. As únicas propriedades que aparecem nos modelos de ligação são as que determinou ser seguras para a entrada do utilizador. Não há nenhuma razão de segurança que impeça o uso de reflexão para copiar todas as propriedades que existem em comum entre estes dois modelos.</li><li>**E quanto a [Bind(Excluir ="â")].&euro; Posso usá-lo em vez de ter modelos de ligação separados? -** Esta abordagem não é recomendada. A utilização [Bind(Excluir ="ââ")]&euro;significa que qualquer novo imóvel é vinculado por defeito. Quando uma nova propriedade é adicionada, há um passo extra para lembrar para manter as coisas seguras, em vez de ter o design seguro por padrão. Dependendo do desenvolvedor verificar esta lista cada vez que uma propriedade é adicionada é arriscado.</li><li>**[Bind(Incluir ="â")]&euro;é útil para operações de Edição?** Não. [A ligação (Incluir ="ââ")]&euro;só é adequada para operações de estilo INSERT (adicionando novos dados). Para operações de estilo UPDATE (revisão de dados existentes), utilize outra abordagem, como ter modelos de ligação separados ou passar uma lista explícita de propriedades permitidas para UpdateModel ou TryUpdateModel. Adicionar um atributo [Bind(Incluir ="â&euro;"")] numa operação edital significa que o MVC criará uma instância de objeto e definirá apenas as propriedades listadas, deixando todas as outras nos seus valores padrão. Quando os dados forem persistidos, substituirá totalmente a entidade existente, repondo os valores de quaisquer propriedades omitidas para os seus incumprimentos. Por exemplo, se a IsAdmin fosse omitida de um atributo [Bind="â")]&euro;numa operação edita, qualquer utilizador cujo nome foi editado através desta ação seria reposto para IsAdmin = falso (qualquer utilizador editado perderia o estatuto de administrador). Se pretender evitar atualizações a determinadas propriedades, utilize uma das outras abordagens acima. Note que algumas versões de ferramentas mVC geram&euro;classes de controladores com [Bind("Incluir ="â")] em ações de Edição e implica que remover uma propriedade dessa lista impedirá ataques de sobre-publicação. No entanto, tal como acima descrito, esta abordagem não funciona como pretendido e, em vez disso, redefinirá quaisquer dados nas propriedades omitidas para os seus valores predefinidos.</li><li>**Para as operações create, existem ressalvas usando&euro;[Bind ="â "â")] em vez de modelos de ligação separados?** Sim, é um pouco. Em primeiro lugar, esta abordagem não funciona para cenários de Edição, exigindo a manutenção de duas abordagens separadas para atenuar todas as vulnerabilidades de sobre-publicação. Em segundo lugar, modelos de ligação separados impõem a separação das preocupações entre&euro;a forma utilizada para a entrada do utilizador e a forma utilizada para a persistência, algo [Bind(Incluir ="â")] não faz. Terceiro, note que [Bind(Incluir&euro;="ââ")] só pode lidar com propriedades de alto nível; não é possível permitir apenas porções de sub-propriedades (como "Details.Name") no atributo. Finalmente, e talvez o mais importante, usando [Bind(Incluir ="â")]&euro;adiciona um passo extra que deve ser lembrado sempre que a classe é usada para a ligação do modelo. Se um novo método de ação se ligar diretamente à classe de dados&euro;e se esquecer de incluir um atributo [Bind(Incluir ="â")",&euro;pode ser vulnerável a ataques de sobre-postagem, pelo que a abordagem [Bind(Incluir ="â")] é um pouco menos segura por defeito. Se utilizar [Ligar ="â&euro;â"),,tenha sempre o cuidado de se lembrar de o especificar sempre que as suas classes de dados aparecem como parâmetros de método de ação.</li><li>**Para as operações create, que tal colocar&euro;o atributo [Bind ="â "â")] na própria classe modelo? Esta abordagem não evita a necessidade de se lembrar de colocar o atributo em todos os métodos de ação? -** Esta abordagem funciona em alguns casos. Utilizar [Bind(Incluir ="â&euro;"")] no próprio modelo (em vez de em parâmetros de ação utilizando esta classe),&euro;evita a necessidade de se lembrar de incluir o atributo [Bind(Incluir ="â")] em todos os métodos de ação. A utilização do atributo diretamente na classe cria efetivamente uma área de superfície separada desta classe para fins de ligação de modelos. No entanto, esta abordagem apenas permite uma forma de ligação modelo por classe modelo. Se um método de ação tiver de permitir a ligação de um campo de modelos (por exemplo, uma ação só para administradorque atualize as funções dos utilizadores) e outras ações devem impedir a ligação de modelos deste campo, esta abordagem não funcionará. Cada classe só pode ter uma forma de ligação modelo; se diferentes ações precisarem de diferentes formas de ligação de modelos, precisam de&euro;representar estas formas separadas utilizando classes de ligação de modelos separadas ou atributos separados [Bind="â")] nos métodos de ação.</li><li>**O que são modelos vinculativos? São a mesma coisa que modelos de visualização? -** Estes são dois conceitos relacionados. O termo modelo de ligação refere-se a uma classe modelo utilizada na lista de parâmetros de uma ação (a forma passada do modelo MVC que liga ao método de ação). O modelo de visão de termo refere-se a uma classe modelo passada de um método de ação para uma vista. A utilização de um modelo específico para a visão é uma abordagem comum para passar dados de um método de ação para uma vista. Muitas vezes, esta forma também é adequada para a ligação do modelo, e o modelo de visualização de termo pode ser usado para referir o mesmo modelo usado em ambos os lugares. Para ser mais preciso, este procedimento fala especificamente sobre modelos vinculativos, centrando-se na forma passada à ação, que é o que importa para fins de atribuição em massa.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Codificar saída web não fidedigna antes da renderização

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, Formulários Web, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Como evitar a escrita cruzada em ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [Scripting cross-site](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Passos** | A escrita transversal (geralmente abreviada como XSS) é um vetor de ataque para serviços online ou qualquer aplicação/componente que consuma a entrada da web. As vulnerabilidades do XSS podem permitir que um intruso execute o script na máquina de outro utilizador através de uma aplicação web vulnerável. Scripts maliciosos podem ser usados para roubar cookies e, de outra forma, adulterar a máquina da vítima através do JavaScript. O XSS é evitado validando a entrada do utilizador, garantindo que está bem formado e codificando antes de ser renderizado numa página web. A validação de entrada e a codificação da saída podem ser feitas utilizando a Biblioteca de Proteção Web. Para o código\#gerido (C, VB.NET, etc.), utilize um ou mais métodos de codificação apropriados da Biblioteca de Proteção Web (Anti-XSS), dependendo do contexto em que a entrada do utilizador é manifestada:| 

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

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Execute validação de entrada e filtragem em todas as propriedades do modelo do tipo de corda

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Adicionar Validação](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [Validação de Dados de Modelos numa Aplicação MVC,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [Princípios Orientadores para as suas aplicações ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | <p>Todos os parâmetros de entrada devem ser validados antes de serem utilizados na aplicação para garantir que a aplicação é salvaguardada contra inputs de utilizador maliciosos. Valide os valores de entrada utilizando validações regulares de expressão no lado do servidor com uma estratégia de validação whitelist. Entradas/parâmetros não analíticos do utilizador passados aos métodos podem causar vulnerabilidades de injeção de código.</p><p>Para aplicações web, os pontos de entrada também podem incluir campos de formulários, QueryStrings, cookies, cabeçalhos HTTP e parâmetros de serviço web.</p><p>Os seguintes controlos de validação de entrada devem ser efetuados mediante encadernação do modelo:</p><ul><li>As propriedades do modelo devem ser anotadas com anotação RegularExpression, para aceitar caracteres permitidos e comprimento máximo admissível</li><li>Os métodos do controlador devem executar a validade do ModelState</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>A sanitização deve ser aplicada em campos de formulárioque aceitam todos os caracteres, por exemplo, editor de texto rico

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Encode Inseguro Input](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [Html Sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Passos** | <p>Identifique todas as etiquetas de marcação estáticaque que deseja utilizar. Uma prática comum é restringir a formatação `<b>` a elementos `<i>` HTML seguros, tais como (arrojado) e (itálico).</p><p>Antes de escrever os dados, codifica-os html. Isto torna qualquer script malicioso seguro, fazendo com que seja tratado como texto, não como código executável.</p><ol><li>Desativar ASP.NET validação de pedidos através da adição \@ do atributo ValidadoSolicit="falso" à diretiva Página</li><li>Codificar a entrada da cadeia com o método HtmlEncode</li><li>Utilize um StringBuilder e ligue para o seu método de substituição para remover seletivamente a codificação dos elementos HTML que pretende permitir</li></ol><p>A página-in das referências desativa `ValidateRequest="false"`ASP.NET validação de pedidos por definição . Codifica a entrada e permite seletivamente a `<b>` `<i>` e Alternativamente, pode também ser utilizada uma biblioteca .NET para a sanitização HTML.</p><p>HtmlSanitizer é uma biblioteca .NET para limpeza de fragmentos HTML e documentos de construções que podem levar a ataques XSS. Usa angleSharp para analisar, manipular e render HTML e CSS. O HtmlSanitizer pode ser instalado como um pacote NuGet, e a entrada do utilizador pode ser transmitida através de métodos de sanitização HTML ou CSS relevantes, conforme aplicável, no lado do servidor. Por favor, note que a Sanitização como um controlo de segurança deve ser considerada apenas como uma última opção.</p><p>A validação de entrada e a codificação de saída são consideradas melhores controlos de segurança.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Não atribuir elementos DOM a sumidouros que não tenham codificação incorporada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Muitas funções javascript não fazem codificação por padrão. Ao atribuir entrada não fidedigna a elementos DOM através de tais funções, pode resultar em execuções de scripts de site cruzado (XSS).| 

### <a name="example"></a>Exemplo
Seguem-se exemplos inseguros: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Não use; `innerHtml` em `innerText`vez disso, use . Da mesma forma, em vez de `$("#elm").html()`, usar`$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Validar todos os redirecionamentos dentro da aplicação são fechados ou feitos com segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [O Quadro de Autorização OAuth 2.0 - Redirectores abertos](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Passos** | <p>A conceção da aplicação que exija a reorientação para um local fornecido pelo utilizador deve limitar os possíveis alvos de reorientação a uma lista predefinida de sites ou domínios "seguros". Todos os redirecionamentos da aplicação devem ser fechados/seguros.</p><p>Para efetuar este procedimento:</p><ul><li>Identificar todos os redirecionamentos</li><li>Implementar uma mitigação adequada para cada redirecionamento. As mitigações apropriadas incluem redirecionar a lista branca ou a confirmação do utilizador. Se um web site ou serviço com uma vulnerabilidade de redirecionamento aberto utilizar fornecedores de identidade Facebook/OAuth/OpenID, um intruso pode roubar o sinal de logon de um utilizador e personificar esse utilizador. Este é um risco inerente ao utilizar a OAuth, que está documentada no RFC 6749 "O Quadro de Autorização da OAuth 2.0", secção 10.15 "Redirecionamentos Abertos" Da mesma forma, as credenciais dos utilizadores podem ser comprometidas por ataques de phishing com recurso a redirecionamentos abertos.</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementar validação de entrada em todos os parâmetros do tipo de cadeia aceites pelos métodos do Controlador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação de Dados de Modelos numa Aplicação MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Princípios Orientadores para o seu ASP.NET Aplicações MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Para métodos que apenas aceitam o tipo de dados primitivos, e não modelos como argumento, a validação da entrada usando expressão regular deve ser feita. Aqui regex.IsMatch deve ser usado com um padrão regex válido. Se a entrada não corresponder à expressão regular especificada, o controlo não deve prosseguir e deve ser apresentado um aviso adequado sobre a falha de validação.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Definir limite limite máximo para o processamento regular de expressão para evitar DoS devido a más expressões regulares

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, Formulários Web, MVC5, MVC6  |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Passos** | Para garantir a negação de ataques de serviço contra expressões regulares mal criadas, que causam muitos retrocessos, definir o tempo de incumprimento global. Se o tempo de processamento demorar mais tempo do que o limite superior definido, lançará uma exceção de timeout. Se nada estiver configurado, o tempo limite seria infinito.| 

### <a name="example"></a>Exemplo
Por exemplo, a seguinte configuração irá lançar uma RegexMatchTimeoutException, se o processamento demorar mais de 5 segundos: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Evite usar html.Raw em vistas de barbear

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Passo | ASP.NET WebPages (Razor) executam a codificação automática de HTML. Todas as cordas impressas por nuggets de código incorporados (@ blocos) são automaticamente codificadas por HTML. No entanto, quando `HtmlHelper.Raw` o Método é invocado, devolve a marcação que não é codificada por HTML. Se `Html.Raw()` for utilizado o método do ajudante, contorna a proteção automática de codificação que a Razor fornece.|

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
Não utilize `Html.Raw()` a não ser que necessite de visualizar a marcação. Este método não executa a codificação de saída implicitamente. Use outros ajudantes ASP.NET, por exemplo,`@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Não utilize consultas dinâmicas em procedimentos armazenados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Um ataque de injeção SQL explora vulnerabilidades na validação de entrada para executar comandos arbitrários na base de dados. Pode ocorrer quando a sua aplicação utiliza a entrada para construir declarações Dinâmicas SQL para aceder à base de dados. Também pode ocorrer se o seu código utilizar procedimentos armazenados que são passadas cordas que contenham entrada bruta do utilizador. Utilizando o ataque de injeção SQL, o intruso pode executar comandos arbitrários na base de dados. Todas as declarações SQL (incluindo as declarações SQL nos procedimentos armazenados) devem ser parametrizadas. As declarações SQL parametrizadas aceitarão caracteres que tenham um significado especial para a SQL (como uma única citação) sem problemas porque são fortemente digitados. |

### <a name="example"></a>Exemplo
Segue-se um exemplo de procedimento armazenado dinâmico inseguro: 

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

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Certifique-se de que a validação do modelo é feita nos métodos Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação de modelos em ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Quando um cliente envia dados para uma API web, é obrigatório validar os dados antes de eterno processamento. Para ASP.NET APIs web que aceitam os modelos como entrada, utilize anotações de dados em modelos para definir regras de validação sobre as propriedades do modelo.|

### <a name="example"></a>Exemplo
O seguinte código demonstra o mesmo: 

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
No método de ação dos controladores API, a validade do modelo deve ser verificada explicitamente, tal como mostrado a seguir: 

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

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementar validação de entrada em todos os parâmetros do tipo de cadeia aceites pelos métodos Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, MVC 5, MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação de Dados de Modelos numa Aplicação MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Princípios Orientadores para o seu ASP.NET Aplicações MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Para métodos que apenas aceitam o tipo de dados primitivos, e não modelos como argumento, a validação da entrada usando expressão regular deve ser feita. Aqui regex.IsMatch deve ser usado com um padrão regex válido. Se a entrada não corresponder à expressão regular especificada, o controlo não deve prosseguir e deve ser apresentado um aviso adequado sobre a falha de validação.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Certifique-se de que os parâmetros de segurança do tipo são utilizados na Web API para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Se utilizar a coleção Parâmetros, a SQL trata a entrada como um valor literal, em vez de como código executável. A recolha de parâmetros pode ser utilizada para impor restrições de tipo e comprimento nos dados de entrada. Valores fora do alcance desencadeiam uma exceção. Se não forem utilizados parâmetros SQL seguros de tipo, os atacantes poderão ser capazes de executar ataques de injeção incorporados na entrada não filtrada.</p><p>Utilize parâmetros seguros do tipo ao construir consultas SQL para evitar possíveis ataques de injeção SQL que podem ocorrer com entrada não filtrada. Pode utilizar parâmetros de tipo seguro com procedimentos armazenados e com declarações SQL dinâmicas. Os parâmetros são tratados como valores literais pela base de dados e não como código executável. Os parâmetros também são verificados para o tipo e o comprimento.</p>|

### <a name="example"></a>Exemplo
O código seguinte mostra como utilizar parâmetros seguros de tipo com a SqlParameterCollection ao chamar um procedimento armazenado. 

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
No exemplo do código anterior, o valor de entrada não pode ser superior a 11 caracteres. Se os dados não estiverem em conformidade com o tipo ou comprimento definido sao a uma exceção. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Use consultas SQL parametrizadas para Cosmos DB

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Documento Azure DB | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Anunciando a parametrização SQL em Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Passos** | Embora o Azure Cosmos DB apenas suporte consultas apenas de leitura, a injeção SQL ainda é possível se as consultas forem construídas concatenando com a entrada do utilizador. Pode ser possível que um utilizador tenha acesso a dados a que não deveria aceder dentro da mesma recolha, elaborando consultas sql maliciosas. Utilize consultas SQL parametrizadas se as consultas forem construídas com base na entrada do utilizador. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>Validação de entrada do WCF através da ligação schema

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Quadro 3 genérico e LÍQUIDO |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Passos** | <p>A falta de validação leva a ataques de injeção de diferentes tipos.</p><p>A validação de mensagens representa uma linha de defesa na proteção da sua aplicação WCF. Com esta abordagem, valida mensagens usando schemas para proteger as operações de serviço wCF de ataques por um cliente malicioso. Valide todas as mensagens recebidas pelo cliente para proteger o cliente de ataques por um serviço malicioso. A validação de mensagens permite validar mensagens quando as operações consomem contratos de mensagens ou contratos de dados, o que não pode ser feito através da validação do parâmetro. A validação de mensagens permite criar lógica de validação dentro de schemas, proporcionando assim mais flexibilidade e reduzindo o tempo de desenvolvimento. Os schemas podem ser reutilizados em diferentes aplicações dentro da organização, criando padrões para a representação de dados. Além disso, a validação de mensagens permite proteger as operações quando consomem tipos de dados mais complexos envolvendo contratos que representam a lógica do negócio.</p><p>Para efetuar a validação de mensagens, constrói primeiro um esquema que representa as operações do seu serviço e os tipos de dados consumidos por essas operações. Em seguida, cria uma classe .NET que implementa um inspetor de mensagens de cliente personalizado e inspetor de mensagens deexpedidores personalizados para validar as mensagens enviadas/recebidas para/do serviço. Em seguida, implementa um comportamento de ponto final personalizado para permitir a validação de mensagens tanto no cliente como no serviço. Finalmente, implementa um elemento de configuração personalizado na classe que lhe permite expor o comportamento final personalizado alargado no ficheiro de configuração do serviço ou do cliente"</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF- Validação de entrada através de Inspetores de Parâmetros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Quadro 3 genérico e LÍQUIDO |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Passos** | <p>A entrada e validação de dados representa uma linha importante de defesa na proteção da sua aplicação WCF. Deverá validar todos os parâmetros expostos nas operações de serviço wCF para proteger o serviço de ataques por um cliente malicioso. Inversamente, deve também validar todos os valores de devolução recebidos pelo cliente para proteger o cliente de ataques por um serviço malicioso</p><p>O WCF fornece diferentes pontos de extibilidade que lhe permitem personalizar o comportamento do tempo de execução do WCF criando extensões personalizadas. Os Inspetores de Mensagens e os Inspetores de Parâmetros são dois mecanismos de extibilidade utilizados para ganhar maior controlo sobre os dados que passam entre um cliente e um serviço. Deve utilizar inspetores de parâmetros para validação de entrada e utilizar inspetores de mensagens apenas quando necessitar de inspecionar toda a mensagem que flui dentro e fora de um serviço.</p><p>Para efetuar a validação de entrada, irá construir uma classe .NET e implementar um inspetor de parâmetros personalizado para validar parâmetros de operações ao seu serviço. Em seguida, implementará um comportamento de ponto final personalizado para permitir a validação tanto no cliente como no serviço. Finalmente, irá implementar um elemento de configuração personalizado na classe que lhe permite expor o comportamento final personalizado alargado no ficheiro de configuração do serviço ou do cliente</p>|
