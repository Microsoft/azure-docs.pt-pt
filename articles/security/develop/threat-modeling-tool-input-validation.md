---
title: Validação de entradas - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a validação de entradas na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b0be672c0768b4facb6518c777d4fe56eb28aa9
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515681"
---
# <a name="security-frame-input-validation--mitigations"></a>Quadro de segurança: Validação de entradas / Mitigações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Desativar a script xSLT para todas as transformações usando folhas de estilo não fided quantos fidedhias](#disable-xslt)</li><li>[Certifique-se de que cada página que pode conter conteúdo controlável do utilizador opta por não cheirar mime automático](#out-sniffing)</li><li>[Resolução de entidades De endurecimento ou Desativação de XML](#xml-resolution)</li><li>[Aplicações que usando http.sys realizar verificação de canonização URL](#app-verification)</li><li>[Certifique-se de que existem controlos adequados ao aceitar ficheiros dos utilizadores](#controls-users)</li><li>[Certifique-se de que os parâmetros de segurança do tipo são usados na Aplicação Web para acesso a dados](#typesafe)</li><li>[Utilize classes de ligação de modelos separadas ou listas de filtros de encadernação para evitar a vulnerabilidade da atribuição em massa do MVC](#binding-mvc)</li><li>[Codificar a saída web não fideditada antes da renderização](#rendering)</li><li>[Executar validação de entrada e filtragem em todas as propriedades do tipo de cadeias](#typemodel)</li><li>[A sanitização deve ser aplicada em campos de formulário que aceitam todos os caracteres, por exemplo, editor de texto rico](#richtext)</li><li>[Não atribuir elementos DOM a pias que não tenham codificação incorporada](#inbuilt-encode)</li><li>[Validar todos os redirecionamentos dentro da aplicação são fechados ou feitos com segurança](#redirect-safe)</li><li>[Implementar validação de entrada em todos os parâmetros do tipo de corda aceites pelos métodos do Controlador](#string-method)</li><li>[Definir prazo limite superior para o processamento regular de expressão para prevenir doS devido a más expressões regulares](#dos-expression)</li><li>[Evite utilizar html.Raw em vistas razor](#html-razor)</li></ul> | 
| **Base de dados** | <ul><li>[Não utilize consultas dinâmicas em procedimentos armazenados](#stored-proc)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a validação do modelo é feita em métodos web de API](#validation-api)</li><li>[Implementar validação de entrada em todos os parâmetros do tipo de cadeia aceites pelos métodos web API](#string-api)</li><li>[Certifique-se de que os parâmetros de segurança do tipo são usados na API web para acesso a dados](#typesafe-api)</li></ul> | 
| **Documento Azure DB** | <ul><li>[Use consultas SQL para a Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Validação de entrada do WCF através da ligação de Schema](#schema-binding)</li><li>[WCF- Validação de entradas através de Inspetores de Parâmetros](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Desativar a script xSLT para todas as transformações usando folhas de estilo não fided quantos fidedhias

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [XSLT Security](/previous-versions/windows/desktop/ms763800(v=vs.85)), [XsltSettings.EnableScript Property](/dotnet/api/system.xml.xsl.xsltsettings.enablescript) |
| **Passos** | O XSLT suporta a scripting dentro das folhas de estilo utilizando o `<msxml:script>` elemento. Isto permite que as funções personalizadas sejam usadas numa transformação XSLT. O script é executado no contexto do processo de realização da transformação. O script XSLT deve ser desativado quando num ambiente não fidedi real para evitar a execução de código não fidedis. *Se utilizar .NET:* A scripting XSLT é desativada por predefinição; no entanto, deve garantir que não foi explicitamente ativado através da `XsltSettings.EnableScript` propriedade.|

### <a name="example"></a>Exemplo 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver a utilizar o MSXML 6.0, a scripting XSLT é desativada por predefinição; no entanto, deve certificar-se de que não foi ativado explicitamente através da propriedade de objetos XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver a utilizar o MSXML 5 ou abaixo, a scripting XSLT é ativada por predefinição e deve desativá-la explicitamente. Desajuste a propriedade do objeto XML DOM AllowXsltScript para falso. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Certifique-se de que cada página que pode conter conteúdo controlável do utilizador opta por não cheirar mime automático

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IE8 Parte de Segurança V - Proteção Abrangente](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **Passos** | <p>Para cada página que possa conter conteúdo controlável do utilizador, deve utilizar o cabeçalho HTTP `X-Content-Type-Options:nosniff` . Para cumprir este requisito, pode definir o cabeçalho necessário página a página apenas para as páginas que possam conter conteúdo controlável pelo utilizador, ou pode defini-lo globalmente para todas as páginas da aplicação.</p><p>Cada tipo de ficheiro entregue a partir de um servidor web tem um [tipo MIME](https://en.wikipedia.org/wiki/Mime_type) associado (também chamado *de tipo de conteúdo)* que descreve a natureza do conteúdo (isto é, imagem, texto, aplicação, etc.)</p><p>O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que o seu conteúdo não deve ser meifrado por MIME. Este cabeçalho foi concebido para atenuar MIME-Sniffing ataques. O suporte para este cabeçalho foi adicionado no Internet Explorer 8 (IE8)</p><p>Apenas os utilizadores do Internet Explorer 8 (IE8) beneficiarão de Opções de Tipo de Conteúdo X. Versões anteriores do Internet Explorer não respeitam atualmente o cabeçalho X-Content-Type-Options</p><p>O Internet Explorer 8 (e mais tarde) são os únicos principais navegadores a implementar uma funcionalidade de opt-out de cheiro mime. Se e quando outros grandes navegadores (Firefox, Safari, Chrome) implementarem funcionalidades semelhantes, esta recomendação será atualizada para incluir sintaxe para esses navegadores também</p>|

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

* Adicione o cabeçalho através do Global Application \_ BeginRequest 

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

* Pode ativar o cabeçalho necessário apenas para páginas específicas, adicionando-o a respostas individuais: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Resolução de entidades De endurecimento ou Desativação de XML

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Expansão da Entidade XML](https://capec.mitre.org/data/definitions/197.html), [XML Negação de Ataques e Defesas de Serviços](/archive/msdn-magazine/2009/november/xml-denial-of-service-attacks-and-defenses), [Visão Geral de Segurança MSXML,](/previous-versions/windows/desktop/ms754611(v=vs.85)) [Melhores Práticas para Assegurar código MSXML](/previous-versions/windows/desktop/ms759188(v=vs.85)), [Referência do Protocolo deDelegamento NSXMLParser,](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html) [Resolução de Referências Externas](/dotnet/standard/data/xml/resolving-external-resources) |
| **Passos**| <p>Embora não seja amplamente utilizado, existe uma característica de XML que permite ao analisador XML expandir macro entidades com valores definidos dentro do próprio documento ou a partir de fontes externas. Por exemplo, o documento pode definir uma entidade "nome de empresa" com o valor "Microsoft", de modo que cada vez que o texto &companyname; " aparece no documento, é automaticamente substituído pelo texto Microsoft. Ou, o documento pode definir uma entidade "MSFTStock" que faz referência a um serviço web externo para obter o valor atual das ações da Microsoft.</p><p>Em seguida, a qualquer momento &MSFTStock; " aparece no documento, é automaticamente substituído pelo preço atual das ações. No entanto, esta funcionalidade pode ser abusada para criar condições de negação de serviço (DoS). Um intruso pode nidificar várias entidades para criar uma bomba XML de expansão exponencial que consome toda a memória disponível no sistema. </p><p>Em alternativa, ele pode criar uma referência externa que transmita uma quantidade infinita de dados ou que simplesmente pendura o fio. Como resultado, todas as equipas devem desativar totalmente a resolução interna e/ou externa da entidade XML se a sua aplicação não a utilizar, ou limitar manualmente a quantidade de memória e tempo que a aplicação pode consumir para resolução de entidades se esta funcionalidade for absolutamente necessária. Se a resolução da entidade não for exigida pela sua aplicação, desative-a. </p>|

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
Note que o valor padrão de `ProhibitDtd` in `XmlReaderSettings` é verdadeiro, mas nele é `XmlTextReader` falso. Se estiver a utilizar XmlReaderSettings, não precisa de definir o Proibidodtd para ser verdadeiro explicitamente, mas é recomendado por razões de segurança que o faça. Note também que a classe XmlDocument permite a resolução da entidade por padrão. 

### <a name="example"></a>Exemplo
Para desativar a resolução da entidade para os XmlDocuments, utilize a `XmlDocument.Load(XmlReader)` sobrecarga do método de carga e desfaça as propriedades apropriadas no argumento XmlReader para desativar a resolução, conforme ilustrado no seguinte código: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exemplo
Se não for possível desativar a resolução da entidade para a sua aplicação, desabrade a propriedade XmlReaderSettings.MaxCharactersFromEntities para um valor razoável de acordo com as necessidades da sua aplicação. Isto irá limitar o impacto de potenciais ataques exponenciais de expansão exponencial do DoS. O seguinte código constitui um exemplo desta abordagem: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exemplo
Se precisa de resolver entidades inline mas não precisar de resolver entidades externas, deslome o XmlReaderSettings.Xmlimóveis resolver para nulo. Por exemplo: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Note que em MSXML6, o ProibiDTD é definido como verdadeiro (desativação do processamento de DTD) por padrão. Para o código Apple OSX/iOS, existem dois parsers XML que pode utilizar: NSXMLParser e libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Aplicações que usando http.sys realizar verificação de canonização URL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Qualquer aplicação que utilize http.sys deve seguir estas orientações:</p><ul><li>Limite o comprimento do URL a não mais de 16.384 caracteres (ASCII ou Unicode). Este é o comprimento máximo absoluto de URL baseado na definição padrão dos Serviços de Informação da Internet (IIS) 6. Os websites devem esforçar-se por um comprimento mais curto do que este, se possível</li><li>Utilize as classes de E/S de ficheiros padrão .NET Framework (como FileStream) uma vez que estas irão tirar partido das regras de canonização no .NET FX</li><li>Construa explicitamente uma lista de admissões conhecidas</li><li>Rejeitar explicitamente os tipos de ficheiros conhecidos que não servirá os rejeitados urlScan: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, impressora, ini, pol, dat files</li><li>Apanhe as seguintes exceções:<ul><li>System.ArgumentException (para nomes de dispositivos)</li><li>Sistema.NotSupportedExcepção (para fluxos de dados)</li><li>System.IO.FileNotFoundException (para ficheiros inválidos escapados)</li><li>System.iO.DirectoryNotFoundException (para dirs inválidos escapados)</li></ul></li><li>*Não* chame para o ficheiro Win32 I/O APIs. Num URL inválido, devolva graciosamente um erro de 400 ao utilizador e regista o erro real.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Certifique-se de que existem controlos adequados ao aceitar ficheiros dos utilizadores

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Upload de ficheiros sem restrições,](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) [tabela de assinatura de ficheiro](https://www.garykessler.net/library/file_sigs.html) |
| **Passos** | <p>Os ficheiros carregados representam um risco significativo para as aplicações.</p><p>O primeiro passo em muitos ataques é conseguir algum código para o sistema ser atacado. Então o ataque só precisa de encontrar uma maneira de executar o código. A utilização de um upload de ficheiros ajuda o atacante a realizar o primeiro passo. As consequências do upload de ficheiros sem restrições podem variar, incluindo a aquisição completa do sistema, um sistema de ficheiros sobrecarregado ou base de dados, reencaminhamento de ataques para sistemas de back-end e simples desfiguração.</p><p>Depende do que a aplicação faz com o ficheiro carregado e especialmente onde está armazenado. Falta a validação lateral do servidor dos uploads de ficheiros. Os seguintes controlos de segurança devem ser implementados para a funcionalidade de Upload de Ficheiros:</p><ul><li>Verificação de extensão de ficheiro (apenas um conjunto válido de tipo de ficheiro permitido deve ser aceite)</li><li>Limite máximo de tamanho de ficheiro</li><li>O ficheiro não deve ser carregado para webroot; a localização deve ser um diretório em unidade não-sistema</li><li>A convenção de nomeação deve ser seguida, de modo a que o nome do ficheiro carregado tenha alguma aleatoriedade, de modo a evitar a substituição de ficheiros</li><li>Os ficheiros devem ser digitalizados para antivírus antes de escreverem no disco</li><li>Certifique-se de que o nome do ficheiro e quaisquer outros metadados (por exemplo, caminho de ficheiro) são validados para caracteres maliciosos</li><li>A assinatura do formato de ficheiro deve ser verificada, para evitar que um utilizador carreque um ficheiro mascarado (por exemplo, carregar um ficheiro exe alterando a extensão para txt)</li></ul>| 

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

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Certifique-se de que os parâmetros de segurança do tipo são usados na Aplicação Web para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Se utilizar a coleção Parâmetros, o SQL trata a entrada como um valor literal e não como código executável. A recolha de parâmetros pode ser usada para impor restrições de tipo e comprimento nos dados de entrada. Valores fora do alcance desencadeiam uma exceção. Se não forem utilizados parâmetros SQL de tipo seguro, os atacantes poderão ser capazes de executar ataques de injeção que estão incorporados na entrada não filtrada.</p><p>Utilize parâmetros de segurança do tipo ao construir consultas SQL para evitar possíveis ataques de injeção DE SQL que possam ocorrer com entrada não filtrada. Pode utilizar parâmetros de segurança com procedimentos armazenados e com declarações dinâmicas de SQL. Os parâmetros são tratados como valores literais pela base de dados e não como código executável. Os parâmetros também são verificados para o tipo e comprimento.</p>|

### <a name="example"></a>Exemplo 
O código que se segue mostra como utilizar parâmetros de segurança do tipo com a SqlParameterCollection quando se chama um procedimento armazenado. 

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
No exemplo de código anterior, o valor de entrada não pode ser superior a 11 caracteres. Se os dados não estiverem em conformidade com o tipo ou comprimento definido pelo parâmetro, a classe SqlParameter abre uma exceção. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Utilize classes de ligação de modelos separadas ou listas de filtros de encadernação para evitar a vulnerabilidade da atribuição em massa do MVC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Atributos de metadados](/dotnet/api/system.componentmodel.dataannotations.metadatatypeattribute), [vulnerabilidade e mitigação de segurança de chaves públicas,](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation) [Guia Completo para Atribuição de Massas em ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Começar com EF usando MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Passos** | <ul><li>**Quando devo procurar vulnerabilidades de sobre-postagem?** As vulnerabilidades de sobre-publicação podem ocorrer em qualquer local onde se vinculem as classes de modelos da entrada do utilizador. Quadros como o MVC podem representar dados de utilizadores em classes personalizadas .NET, incluindo objetos CLR Simples (POCOs). O MVC povoa automaticamente estas classes de modelos com dados do pedido, proporcionando uma representação conveniente para lidar com a entrada do utilizador. Quando estas classes incluem propriedades que não devem ser definidas pelo utilizador, a aplicação pode ser vulnerável a ataques de sobre-postagem, que permitem o controlo do utilizador de dados que a aplicação nunca pretendeu. Tal como a ligação do modelo MVC, tecnologias de acesso a bases de dados, tais como mappers de objetos/relacionais como o Entity Framework, muitas vezes também suportam o uso de objetos POCO para representar dados de base de dados. Estas classes de modelos de dados fornecem a mesma conveniência no tratamento dos dados de base de dados que a MVC faz no tratamento da entrada do utilizador. Como tanto o MVC como a base de dados suportam modelos semelhantes, como objetos POCO, parece fácil reutilizar as mesmas classes para ambos os fins. Esta prática não preserva a separação de preocupações, e é uma área comum onde as propriedades não intencionais são expostas à ligação de modelos, permitindo ataques de sobre-postagem.</li><li>**Por que não usar as minhas aulas de modelo de base de dados não filtradas como parâmetros para as minhas ações de MVC?** Porque a ligação do modelo MVC vai ligar qualquer coisa nessa classe. Mesmo que os dados não apareçam na sua visão, um utilizador malicioso pode enviar um pedido HTTP com estes dados incluídos, e o MVC irá ligá-lo de bom grado porque a sua ação diz que a classe de base de dados é a forma dos dados que deve aceitar para a entrada do utilizador.</li><li>**Por que me importaria com a forma usada para a ligação de modelos?** A utilização de ASP.NET de um modelo MVC com modelos demasiado amplos expõe uma aplicação a ataques de sobreposição. A sobre-publicação poderia permitir que os atacantes alterassem os dados da aplicação para além do pretendido pelo desenvolvedor, como por exemplo, sobrevam o preço de um item ou os privilégios de segurança de uma conta. As aplicações devem utilizar modelos de ligação específicos de ação (ou listas específicas de filtros de propriedade) para fornecer um contrato explícito para o que a entrada não fidedíssquica permitir através da ligação do modelo.</li><li>**Ter modelos de ligação separados é apenas um código duplicado?** Não, é uma questão de separação de preocupações. Se reutilizar os modelos de base de dados em métodos de ação, está a dizer que qualquer propriedade (ou sublote) dessa classe pode ser definida pelo utilizador num pedido HTTP. Se não é isso que pretende que o MVC faça, precisa de uma lista de filtros ou de uma forma de classe separada para mostrar ao MVC quais os dados que podem vir da entrada do utilizador.</li><li>**Se tiver modelos de ligação separados para a entrada do utilizador, tenho de duplicar todos os meus atributos de anotação de dados?** Não necessariamente. Pode utilizar o MetadataTypeAttribute na classe modelo de base de dados para ligar aos metadados numa classe de ligação modelo. Basta ter em atenção que o tipo referenciado pelo MetadataTypeAttribute deve ser um subconjunto do tipo de referência (pode ter menos propriedades, mas não mais).</li><li>**Mover dados para trás e para a frente entre os modelos de entrada do utilizador e os modelos de base de dados é aborrecido. Posso copiar todas as propriedades usando reflexo?** Sim, é um pouco. As únicas propriedades que aparecem nos modelos de encadernação são as que determinou serem seguras para a entrada do utilizador. Não há nenhuma razão de segurança que impeça o uso de reflexão para copiar todas as propriedades que existem em comum entre estes dois modelos.</li><li>**E quanto a [Bind(Excluir &euro; =""""")]. Posso usar isso em vez de ter modelos de ligação separados? -** Esta abordagem não é recomendada. A utilização [Desresu por defeito (Exclui &euro; =""")] significa que qualquer nova propriedade é vinculada por defeito. Quando uma nova propriedade é adicionada, há um passo extra para lembrar para manter as coisas seguras, em vez de ter o design seguro por padrão. Dependendo do desenvolvedor verificar esta lista cada vez que uma propriedade é adicionada é arriscado.</li><li>**[Bind (Incluir &euro; =""""")] é útil para operações de edição?** [O encaixe (Incluir &euro; =""""")] só é adequado para operações de inserção (adicionando novos dados). Para operações de estilo UPDATE (revisão dos dados existentes), utilize outra abordagem, como ter modelos de ligação separados ou passar uma lista explícita de propriedades permitidas para UpdateModel ou TryUpdateModel. Adicionar um atributo [Bind &euro; =""""")] numa operação de Edição significa que o MVC criará uma instância de objeto e definirá apenas as propriedades listadas, deixando todas as outras nos seus valores padrão. Quando os dados são persistidos, substituirá totalmente a entidade existente, repondo os valores de quaisquer propriedades omitidas aos seus padrão. Por exemplo, se o IsAdmin fosse omitido de um atributo [Incluindo &euro; ="""""")] numa operação de Edição, qualquer utilizador cujo nome tenha sido editado através desta ação seria reposto para IsAdmin = falso (qualquer utilizador editado perderia o estatuto de administrador). Se pretender evitar atualizações a determinadas propriedades, utilize uma das outras abordagens acima. Note que algumas versões de ferramentas MVC geram classes de controladores com [Bind ("Include &euro; =""""")] em ações de Edição e implicam que remover uma propriedade dessa lista impedirá ataques de sobre-postagem. No entanto, tal como acima descrito, esta abordagem não funciona como pretendido e, em vez disso, irá redefinir quaisquer dados nas propriedades omitidas aos seus valores predefinidos.</li><li>**Para as operações de criação, existem ressalvas usando [Incluir &euro; ="""")] em vez de modelos de ligação separados?** Em primeiro lugar, esta abordagem não funciona para os cenários de Edição, exigindo a manutenção de duas abordagens separadas para mitigar todas as vulnerabilidades de sobre-posting. Em segundo lugar, modelos de ligação separados impõem a separação das preocupações entre a forma utilizada para a entrada do utilizador e a forma utilizada para a persistência, algo que [Bind &euro; =""".")] não faz. Em terceiro lugar, note que [Bind (Incluir &euro; =""""")] só pode lidar com propriedades de nível superior; não pode permitir apenas partes de sub-propriedades (como "Details.Name") no atributo. Finalmente, e talvez o mais importante, usar [Bind (Incluído &euro; ="""")] adiciona um passo extra que deve ser lembrado sempre que a classe é usada para a ligação do modelo. Se um novo método de ação se ligar diretamente à classe de dados e se esquecer de incluir um atributo [Incluindo &euro; ="""."), pode ser vulnerável a ataques sobre-postais, pelo que a abordagem [Bind &euro; =""""")] é um pouco menos segura por padrão. Se utilizar [Bind (Incluir &euro; ="""")], tenha sempre o cuidado de se lembrar de especirá-lo sempre que as suas classes de dados aparecerem como parâmetros do método de ação.</li><li>**Para as operações de criação, que tal colocar o atributo [Bind &euro; =""""")] na própria classe modelo? Esta abordagem não evita a necessidade de se lembrar de colocar o atributo em todos os métodos de ação? -** Esta abordagem funciona em alguns casos. A utilização [Bind (Incluir &euro; ="""")] no próprio tipo de modelo (em vez de em parâmetros de ação utilizando esta classe), evita a necessidade de se lembrar de incluir o atributo [Incluir &euro; ="""")] em todos os métodos de ação. A utilização do atributo diretamente na classe cria efetivamente uma área de superfície separada desta classe para fins de ligação do modelo. No entanto, esta abordagem só permite uma forma de ligação modelo por classe de modelo. Se um método de ação tiver de permitir a ligação do modelo de um campo (por exemplo, uma ação apenas para administrador que atualiza as funções dos utilizadores) e outras ações devem impedir a ligação do modelo deste campo, esta abordagem não funcionará. Cada classe só pode ter uma forma de encadernação de modelo; se diferentes ações precisarem de diferentes formas de ligação do modelo, precisam de representar estas formas separadas utilizando classes de ligação de modelos separadas ou atributos separados [Incluindo &euro; =""".")] nos métodos de ação.</li><li>**O que são modelos de encadernação? São a mesma coisa que os modelos de visualização? -** São dois conceitos relacionados. O modelo de ligação do termo refere-se a uma classe modelo utilizada na lista de parâmetros de uma ação (a forma passada do modelo MVC ligando-se ao método de ação). O modelo de visualização do termo refere-se a uma classe modelo passada de um método de ação para uma visão. A utilização de um modelo específico da vista é uma abordagem comum para passar dados de um método de ação para uma visão. Muitas vezes, esta forma também é adequada para a encadernação do modelo, e o modelo de vista de fim pode ser usado para referir o mesmo modelo usado em ambos os lugares. Para ser mais preciso, este procedimento fala especificamente sobre modelos vinculativos, centrando-se na forma transmitida à ação, que é o que importa para fins de atribuição em massa.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Codificar a saída web não fideditada antes da renderização

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, Formulários Web, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Como prevenir a scripting cross-site em ASP.NET,](/previous-versions/msp-n-p/ff649310(v=pandp.10)) [Scripting cross-site](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Passos** | A scripting cross-site (geralmente abreviada como XSS) é um vetor de ataque para serviços online ou qualquer aplicação/componente que consome a entrada da web. As vulnerabilidades do XSS podem permitir que um intruso execute o script na máquina de outro utilizador através de uma aplicação web vulnerável. Scripts maliciosos podem ser usados para roubar cookies e de outra forma adulterar a máquina da vítima através do JavaScript. O XSS é impedido por validar a entrada do utilizador, certificando-se de que está bem formado e codificado antes de ser renderizado numa página web. A validação de entradas e codificação de saída pode ser feita utilizando a Biblioteca de Proteção Web. Para código gerido (C \# , VB.NET, etc.), utilize um ou mais métodos de codificação apropriados da Biblioteca de Proteção Web (Anti-XSS), dependendo do contexto em que a entrada do utilizador se manifeste:| 

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

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Executar validação de entrada e filtragem em todas as propriedades do tipo de cadeias

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Adicionar validação](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [validar dados de modelos numa aplicação MVC,](/previous-versions/dd410404(v=vs.90)) [princípios orientadores para as suas aplicações de MVC ASP.NET](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Passos** | <p>Todos os parâmetros de entrada devem ser validados antes de serem utilizados na aplicação para garantir que a aplicação é protegida contra entradas de utilizador maliciosas. Valide os valores de entrada utilizando validações de expressão regulares no lado do servidor com uma estratégia de validação de lista permitida. Entradas/parâmetros de utilizador nãoanitizados passados para os métodos podem causar vulnerabilidades de injeção de código.</p><p>Para aplicações web, os pontos de entrada também podem incluir campos de formulários, QueryStrings, cookies, cabeçalhos HTTP e parâmetros de serviço web.</p><p>Os seguintes controlos de validação de entradas devem ser efetuados após a ligação do modelo:</p><ul><li>As propriedades do modelo devem ser anotadas com anotação regular de expressão, para aceitar caracteres permitidos e comprimento máximo admissível</li><li>Os métodos de controlador devem executar a validade do ModelState</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>A sanitização deve ser aplicada em campos de formulário que aceitam todos os caracteres, por exemplo, editor de texto rico

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Codificar entrada insegura](/previous-versions/msp-n-p/ff647397(v=pandp.10)#paght000003_step3), [desinfetante HTML](https://github.com/mganss/HtmlSanitizer) |
| **Passos** | <p>Identifique todas as etiquetas de marcação estática que pretende utilizar. Uma prática comum consiste em restringir a formatação a elementos HTML seguros, tais como `<b>` (ousados) e `<i>` (itálico).</p><p>Antes de escrever os dados, o HTML codifica-os. Isto torna qualquer script malicioso seguro, fazendo com que seja tratado como texto, e não como código executável.</p><ol><li>Desativar ASP.NET validação de pedido através da adição do atributo ValideRequest="falso" à \@ diretiva Página</li><li>Codificar a entrada de cadeia com o método HtmlEncode</li><li>Utilize um StringBuilder e chame o seu método de substituição para remover seletivamente a codificação nos elementos HTML que pretende permitir</li></ol><p>A página nas referências desativa ASP.NET solicitar validação por definição `ValidateRequest="false"` . O HTML codifica a entrada e permite `<b>` seletivamente a `<i>` e, em alternativa, uma biblioteca .NET para saneamento HTML.</p><p>HtmlSanitizer é uma biblioteca .NET para a limpeza de fragmentos e documentos HTML de construções que podem levar a ataques XSS. Utiliza angleSharp para analisar, manipular e renderizar HTML e CSS. HtmlSanitizer pode ser instalado como um pacote NuGet, e a entrada do utilizador pode ser transmitida através de métodos de saneamento HTML ou CSS relevantes, conforme aplicável, no lado do servidor. Por favor, note que a higienização como um controlo de segurança deve ser considerada apenas como uma última opção.</p><p>A validação de entradas e a codificação de saída são consideradas melhores controlos de segurança.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Não atribuir elementos DOM a pias que não tenham codificação incorporada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Muitas funções javascript não codificam por defeito. Ao atribuir entradas não fidedusquias a elementos DOM através de tais funções, pode resultar em execuções de scripts de sítio cruzado (XSS).| 

### <a name="example"></a>Exemplo
Seguem-se exemplos inseguros: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Não `innerHtml` utilize; em vez disso, use `innerText` . Da mesma forma, em vez `$("#elm").html()` de, usar `$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Validar todos os redirecionamentos dentro da aplicação são fechados ou feitos com segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [O Quadro de Autorização OAuth 2.0 - Redirecionadores abertos](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Passos** | <p>O design da aplicação que requer reorientação para um local fornecido pelo utilizador deve limitar os possíveis alvos de redirecionamento a uma lista "segura" predefinida de sites ou domínios. Todos os redirecionamentos da aplicação devem ser fechados/seguros.</p><p>Para efetuar este procedimento:</p><ul><li>Identificar todos os redirecionamentos</li><li>Implementar uma mitigação adequada para cada redirecionamento. As mitigações adequadas incluem a lista permitida de redirecionamentos ou a confirmação do utilizador. Se um web site ou serviço com uma vulnerabilidade de redirecionamento aberto utilizar fornecedores de identidade Facebook/OAuth/OpenID, um intruso pode roubar o token de início de sposição de um utilizador e personificar esse utilizador. Este é um risco inerente ao utilizar o OAuth, que é documentado no RFC 6749 "O Quadro de Autorização OAuth 2.0", secção 10.15 "Redirecionamentos Abertos" Da mesma forma, as credenciais dos utilizadores podem ser comprometidas por ataques de phishing de lanças usando redirecionamentos abertos</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementar validação de entrada em todos os parâmetros do tipo de corda aceites pelos métodos do Controlador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação de dados de modelos numa aplicação MVC,](/previous-versions/dd410404(v=vs.90)) [princípios orientadores para as suas aplicações de MVC ASP.NET](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Passos** | Para métodos que apenas aceitam o tipo de dados primitivo, e não modelos como argumento, a validação de entrada usando a Expressão Regular deve ser feita. Aqui o Regex.IsMatch deve ser utilizado com um padrão regex válido. Se a entrada não corresponder à expressão regular especificada, o controlo não deve prosseguir e deve ser apresentado um aviso adequado relativamente à falha de validação.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Definir prazo limite superior para o processamento regular de expressão para prevenir doS devido a más expressões regulares

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, Formulários Web, MVC5, MVC6  |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade DefaultRegexMatchTimeout](/dotnet/api/system.web.configuration.httpruntimesection.defaultregexmatchtimeout) |
| **Passos** | Para garantir a negação de ataques de serviço contra expressões regulares mal criadas, que causam muito retrocesso, definir o tempo limite de incumprimento global. Se o tempo de processamento demorar mais do que o limite superior definido, lançaria uma exceção timeout. Se nada estiver configurado, o tempo limite seria infinito.| 

### <a name="example"></a>Exemplo
Por exemplo, a seguinte configuração lançará uma RegexMatchTimeoutException, se o processamento demorar mais de 5 segundos: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Evite utilizar html.Raw em vistas razor

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Passo | ASP.NET WebPages (Razor) executam codificação html automática. Todas as cordas impressas por nuggets de código incorporados (@ blocos) são codificadas automaticamente por HTML. No entanto, quando `HtmlHelper.Raw` o Método é invocado, devolve a marcação que não é DESCODIficado. Se `Html.Raw()` for utilizado o método do ajudante, contorna a proteção de codificação automática que a Razor fornece.|

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
Não utilize `Html.Raw()` a menos que precise de visualizar a marcação. Este método não executa a codificação de saída implicitamente. Utilize outros ASP.NET ajudantes, por exemplo, `@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Não utilize consultas dinâmicas em procedimentos armazenados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Um ataque de injeção SQL explora vulnerabilidades na validação de entradas para executar comandos arbitrários na base de dados. Pode ocorrer quando a sua aplicação utiliza a entrada para construir declarações dinâmicas de SQL para aceder à base de dados. Também pode ocorrer se o seu código utilizar procedimentos armazenados que são fios passados que contêm entrada crua do utilizador. Utilizando o ataque de injeção SQL, o intruso pode executar comandos arbitrários na base de dados. Todas as declarações SQL (incluindo as declarações SQL em procedimentos armazenados) devem ser parametrizadas. As declarações de SQL parametrizadas aceitarão caracteres que tenham um significado especial para SQL (como uma única citação) sem problemas porque são fortemente dactilografados. |

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

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Certifique-se de que a validação do modelo é feita em métodos web de API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação de modelos em ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Quando um cliente envia dados para uma API web, é obrigatório validar os dados antes de então fazer qualquer processamento. Para ASP.NET APIs web que aceitam os modelos como entrada, use anotações de dados em modelos para definir regras de validação sobre as propriedades do modelo.|

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
No método de ação dos controladores API, a validade do modelo deve ser explicitamente verificada como indicado a seguir: 

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

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementar validação de entrada em todos os parâmetros do tipo de cadeia aceites pelos métodos web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérico, MVC 5, MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação de dados de modelos numa aplicação MVC,](/previous-versions/dd410404(v=vs.90)) [princípios orientadores para as suas aplicações de MVC ASP.NET](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Passos** | Para métodos que apenas aceitam o tipo de dados primitivo, e não modelos como argumento, a validação de entrada usando a Expressão Regular deve ser feita. Aqui o Regex.IsMatch deve ser utilizado com um padrão regex válido. Se a entrada não corresponder à expressão regular especificada, o controlo não deve prosseguir e deve ser apresentado um aviso adequado relativamente à falha de validação.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Certifique-se de que os parâmetros de segurança do tipo são usados na API web para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Se utilizar a coleção Parâmetros, o SQL trata a entrada como um valor literal e não como código executável. A recolha de parâmetros pode ser usada para impor restrições de tipo e comprimento nos dados de entrada. Valores fora do alcance desencadeiam uma exceção. Se não forem utilizados parâmetros SQL de tipo seguro, os atacantes poderão ser capazes de executar ataques de injeção que estão incorporados na entrada não filtrada.</p><p>Utilize parâmetros de segurança do tipo ao construir consultas SQL para evitar possíveis ataques de injeção DE SQL que possam ocorrer com entrada não filtrada. Pode utilizar parâmetros de segurança com procedimentos armazenados e com declarações dinâmicas de SQL. Os parâmetros são tratados como valores literais pela base de dados e não como código executável. Os parâmetros também são verificados para o tipo e comprimento.</p>|

### <a name="example"></a>Exemplo
O código que se segue mostra como utilizar parâmetros de segurança do tipo com a SqlParameterCollection quando se chama um procedimento armazenado. 

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
No exemplo de código anterior, o valor de entrada não pode ser superior a 11 caracteres. Se os dados não estiverem em conformidade com o tipo ou comprimento definido pelo parâmetro, a classe SqlParameter abre uma exceção. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Utilize consultas SQL para o Cosmos DB

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Documento Azure DB | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Anunciando a Parametrização SQL em Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Passos** | Embora a Azure Cosmos DB apenas suporte consultas só de leitura, a injeção de SQL ainda é possível se as consultas forem construídas através da concatenação com a entrada do utilizador. Pode ser possível que um utilizador tenha acesso a dados a que não deve aceder dentro da mesma coleção através da criação de consultas SQL maliciosas. Utilize consultas SQL parametrizadas se as consultas forem construídas com base na entrada do utilizador. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>Validação de entrada do WCF através da ligação de Schema

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Quadro genérico e LÍQUIDO 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff647820(v=pandp.10)) |
| **Passos** | <p>A falta de validação leva a diferentes ataques de injeção de tipo.</p><p>A validação de mensagens representa uma linha de defesa na proteção da sua aplicação WCF. Com esta abordagem, você valida mensagens usando esquemas para proteger as operações de serviço wcf de ataque por um cliente mal-intencionado. Validar todas as mensagens recebidas pelo cliente para proteger o cliente de ataques por um serviço malicioso. A validação de mensagens permite validar mensagens quando as operações consomem contratos de mensagens ou contratos de dados, o que não pode ser feito através da validação de parâmetros. A validação de mensagens permite criar lógica de validação dentro dos esquemas, proporcionando assim mais flexibilidade e reduzindo o tempo de desenvolvimento. Os esquemas podem ser reutilizados em diferentes aplicações dentro da organização, criando padrões para a representação de dados. Além disso, a validação de mensagens permite-lhe proteger as operações quando consomem tipos de dados mais complexos envolvendo contratos que representam a lógica do negócio.</p><p>Para realizar a validação de mensagens, primeiro constrói um esquema que representa as operações do seu serviço e os tipos de dados consumidos por essas operações. Em seguida, cria uma classe .NET que implementa um inspetor de mensagens personalizadas do cliente e inspetor de mensagens de despacho personalizado para validar as mensagens enviadas/recebidas para/do serviço. Em seguida, implementa um comportamento de ponto final personalizado para ativar a validação de mensagens tanto no cliente como no serviço. Finalmente, implementa um elemento de configuração personalizado na classe que lhe permite expor o comportamento de ponta personalizado alargado no ficheiro de configuração do serviço ou do cliente"</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF- Validação de entradas através de Inspetores de Parâmetros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Quadro genérico e LÍQUIDO 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff647875(v=pandp.10)) |
| **Passos** | <p>A validação de entradas e dados representa uma importante linha de defesa na proteção da sua aplicação WCF. Deve validar todos os parâmetros expostos nas operações de serviço do WCF para proteger o serviço de ataques por parte de um cliente mal-intencionado. Inversamente, deve também validar todos os valores de devolução recebidos pelo cliente para proteger o cliente de ataques por um serviço malicioso</p><p>O WCF fornece diferentes pontos de extensibilidade que lhe permitem personalizar o comportamento de execução do WCF criando extensões personalizadas. Os Inspetores de Mensagens e Os Inspetores de Parâmetros são dois mecanismos de extensibilidade utilizados para ganhar maior controlo sobre os dados que passam entre um cliente e um serviço. Deve utilizar os inspetores de parâmetros para validação de entradas e utilizar os inspetores de mensagens apenas quando precisar de inspecionar toda a mensagem que flui dentro e fora de um serviço.</p><p>Para realizar a validação de entradas, você construirá uma classe .NET e implementará um inspetor de parâmetros personalizados de forma a validar parâmetros sobre operações ao seu serviço. Em seguida, implementará um comportamento de ponto final personalizado para permitir a validação tanto no cliente como no serviço. Finalmente, implementará um elemento de configuração personalizado na classe que lhe permite expor o comportamento de ponto final personalizado alargado no ficheiro de configuração do serviço ou do cliente</p>|