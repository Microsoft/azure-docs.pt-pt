---
title: Autenticação e autorização com coleções de área de trabalho do Power BI | Documentos da Microsoft
description: Autenticação e autorização com coleções de área de trabalho do Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: e9b240a17614593cd04280a13e006c4d06e832fd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904287"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autenticação e autorização com coleções de área de trabalho do Power BI

Utilização de coleções de área de trabalho de BI de energia **chaves** e **Tokens de aplicação** para autenticação e autorização, em vez da autenticação explícita do utilizador final. Nesse modelo, o seu aplicativo gere a autenticação e autorização para os utilizadores finais. Quando for necessário, a aplicação cria e envia os tokens de aplicação que dizem ao nosso serviço para processar o relatório pedido. Esta estrutura não requer a sua aplicação para utilizar o Azure Active Directory para autenticação de utilizador e a autorização, embora ainda possa.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Duas formas de autenticar

**Chave** -pode utilizar as chaves para todas as chamadas de API de REST de coleções de área de trabalho do Power BI. As chaves podem ser encontradas no **portal do Microsoft Azure** ao selecionar **todas as definições** e, em seguida **chaves de acesso**. Trate sempre a sua chave de como se fosse uma palavra-passe. Essas chaves tem permissões para efetuar qualquer API REST chamar numa coleção de área de trabalho específica.

Para utilizar uma chave numa chamada REST, adicione o seguinte cabeçalho de autorização:

    Authorization: AppKey {your key}

**Token de aplicação** -tokens de aplicação são utilizados para todos os pedidos de incorporação. Foram concebidos para serem executados lado do cliente. O token é restrito a um único relatório e a melhor prática para definir um prazo de expiração.

Tokens de aplicação são um JWT (JSON Web Token) que esteja assinado por uma das suas chaves.

O token de aplicação pode conter as seguintes declarações:

| Afirmação | Descrição |    
| --- | --- |
| **ver** |A versão do token de aplicação. 0.2.0 é a versão atual. |
| **aud** |O destinatário do token. Para utilizar as coleções de área de trabalho do Power BI: *https:\//analysis.windows.net/powerbi/api*. |
| **iss** |Uma cadeia que indica que o aplicativo que emitiu o token. |
| **tipo** |O tipo de token de aplicação que está a ser criada. O tipo de suporte único atual é **incorporar**. |
| **wcn** |O nome de coleção de área de trabalho o token está a ser emitido para. |
| **wid** |O token de ID de área de trabalho é que está a ser emitido. |
| **rid** |O token de ID do relatório está a ser emitido para. |
| **nome de utilizador** (opcional) |Utilizado com RLS, o nome de utilizador é uma cadeia de caracteres que pode ajudar a identificar o utilizador ao aplicar regras RLS. |
| **funções** (opcional) |Uma cadeia de caracteres que contém as funções para selecionar ao aplicar regras de segurança de nível de linha. Se passar mais de uma função, devem ser passadas como uma matriz de sting. |
| **SCP** (opcional) |Uma cadeia de caracteres contendo os âmbitos de permissões. Se passar mais de uma função, devem ser passadas como uma matriz de sting. |
| **EXP** (opcional) |Indica a hora em que o token expira. O valor deve ser passado como carimbos de data / Unix. |
| **nbf** (optional) |Indica a hora em que o token começa a ser válido. O valor deve ser passado como carimbos de data / Unix. |

Um token de aplicação de exemplo é semelhante a:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Quando decodificada, ele é parecido com:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Métodos estão disponíveis dentro os SDKs que facilitam a criação de tokens de aplicação. Por exemplo, para o .NET pode observar os [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) classe e o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) métodos.

Para o SDK de .NET, pode consultar [âmbitos](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Âmbitos

Quando utilizar tokens de incorporação, pode querer restringir a utilização dos recursos que proporcionar o acesso. Por esse motivo, pode gerar um token com permissões de âmbito.

Seguem-se os âmbitos disponíveis para as coleções de área de trabalho do Power BI.

|Âmbito|Descrição|
|---|---|
|Dataset.Read|Fornece a permissão para ler o conjunto de dados especificado.|
|Dataset.Write|Fornece a permissão de escrita para o conjunto de dados especificado.|
|Dataset.ReadWrite|Fornece a permissão para ler e escrever para o conjunto de dados especificado.|
|Report.Read|Fornece a permissão para ver o relatório especificado.|
|Report.ReadWrite|Fornece a permissão para ver e editar o relatório especificado.|
|Workspace.Report.Create|Fornece a permissão para criar um novo relatório na área de trabalho especificada.|
|Workspace.Report.Copy|Fornece a permissão para clonar um relatório existente na área de trabalho especificada.|

Pode fornecer vários âmbitos, utilizando um espaço entre os âmbitos semelhante ao seguinte.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Declarações necessárias - âmbitos**

SCP: {scopesClaim} scopesClaim pode ser uma cadeia ou matriz de cadeias de caracteres, observar as permissões concedidas a recursos de área de trabalho (relatório, conjunto de dados, etc.)

Um token decodificado, com âmbitos definidos, teria uma aparência semelhante a:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Operações e âmbitos

|Operação|Recurso de destino|Permissões de token|
|---|---|---|
|Crie um novo relatório com base num conjunto de dados de (na memória).|Conjunto de dados|Dataset.Read|
|Criar um novo relatório com base num conjunto de dados de (na memória) e guarde o relatório.|Conjunto de dados|* Dataset.Read<br>* Workspace.Report.Create|
|Ver e explorar/Editar (na memória) um relatório existente. Report.Read implica Dataset.Read. Report.Read não permite a guardar edições.|Relatório|Report.Read|
|Editar e guardar um relatório existente.|Relatório|Report.ReadWrite|
|Guarde uma cópia de um relatório (Guardar como).|Relatório|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Eis como funciona o fluxo
1. Copie as chaves de API à sua aplicação. Pode obter as chaves **portal do Azure**.
   
    ![Onde encontrar as chaves de API no portal do Azure](media/get-started-sample/azure-portal.png)
1. Token declara uma afirmação e tem uma hora de expiração.
   
    ![Fluxo do token da aplicação - declarações do token de afirmação](media/get-started-sample/token-2.png)
1. Token é assinado com um chaves de acesso de API.
   
    ![Fluxo token da aplicação - token obtém a sessão iniciado](media/get-started-sample/token-3.png)
1. Pedidos de utilizador para ver um relatório.
   
    ![Fluxo do token da aplicação - utilizador pede para ver um relatório](media/get-started-sample/token-4.png)
1. Token é validado com um chaves de acesso de API.
   
   ![Fluxo token da aplicação - token é validado](media/get-started-sample/token-5.png)
1. As coleções de área de trabalho do Power BI envia um relatório para o utilizador.
   
   ![Fluxo do token da aplicação - serviço Enviar relatório para utilizador](media/get-started-sample/token-6.png)

Após **coleções de área de trabalho do Power BI** envia um relatório para o utilizador, o utilizador pode ver o relatório na sua aplicação personalizada. Por exemplo, se tiver importado os [exemplo de análise PBIX de dados de vendas](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), aplicação web de exemplo teria o seguinte aspeto:

![Exemplo de relatório incorporado na aplicação](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Consultar Também

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Introdução ao exemplo de coleções de área de trabalho do Microsoft Power BI](get-started-sample.md)  
[Cenários comuns de coleções de área de trabalho do Microsoft Power BI](scenarios.md)  
[Começar a utilizar coleções de área de trabalho do Microsoft Power BI](get-started.md)  
[Repositório de Git do Power BI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
