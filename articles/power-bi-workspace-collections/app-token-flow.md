---
title: Autenticar e autorizar Power BI coleções de espaço de trabalho
description: Autenticando e autorizando com Power BI coleções de espaço de trabalho.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8fcd7caffb041c57090d7256361421cb49a9a5fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427119"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autenticando e autorizando com Power BI coleções de espaço de trabalho

Power BI coleções de espaço de trabalho usam **chaves** e **tokens de aplicativo** para autenticação e autorização, em vez de autenticação explícita do usuário final. Nesse modelo, seu aplicativo gerencia a autenticação e a autorização para os usuários finais. Quando necessário, seu aplicativo cria e envia os tokens de aplicativo que dizem ao nosso serviço para renderizar o relatório solicitado. Esse design não exige que seu aplicativo use Azure Active Directory para autenticação e autorização de usuário, embora você ainda possa.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Duas maneiras de autenticar

**Chave** -você pode usar chaves para todas as chamadas de API REST de coleções de espaço de trabalho Power bi. As chaves podem ser encontradas no **portal do Microsoft Azure** selecionando **todas as configurações** e, em seguida, chaves de **acesso**. Sempre trate sua chave como se fosse uma senha. Essas chaves têm permissões para fazer qualquer chamada à API REST em uma coleção de espaços de trabalho específica.

Para usar uma chave em uma chamada REST, adicione o seguinte cabeçalho de autorização:

    Authorization: AppKey {your key}

**Token de aplicativo** -tokens de aplicativo são usados para todas as solicitações de inserção. Elas foram projetadas para serem executadas no lado do cliente. O token é restrito a um único relatório e sua prática recomendada para definir um tempo de expiração.

Tokens de aplicativo são um JWT (token Web JSON) assinado por uma de suas chaves.

O token do aplicativo pode conter as seguintes declarações:

| Afirmação | Descrição |    
| --- | --- |
| **versão** |A versão do token do aplicativo. 0.2.0 é a versão atual. |
| **AUD** |O destinatário pretendido do token. Para Power BI coleções de espaço de trabalho, use: *https:\//Analysis.Windows.net/powerbi/API*. |
| **ISS** |Uma cadeia de caracteres que indica o aplicativo que emitiu o token. |
| **tipo** |O tipo de token de aplicativo que está sendo criado. Atualmente, o único tipo com suporte é **embed**. |
| **wcn** |Nome da coleção de espaços de trabalho para o qual o token está sendo emitido. |
| **wid** |ID do espaço de trabalho para a qual o token está sendo emitido. |
| **eliminá** |ID do relatório para o qual o token está sendo emitido. |
| **nome de usuário** (opcional) |Usado com RLS, username é uma cadeia de caracteres que pode ajudar a identificar o usuário ao aplicar regras de RLS. |
| **funções** (opcional) |Uma cadeia de caracteres que contém as funções a serem selecionadas ao aplicar regras de Segurança em Nível de Linha. Se você passar mais de uma função, elas deverão ser passadas como uma matriz de Stinger. |
| **SCP** (opcional) |Uma cadeia de caracteres que contém os escopos de permissões. Se você passar mais de uma função, elas deverão ser passadas como uma matriz de Stinger. |
| **exp** (opcional) |Indica a hora em que o token expira. O valor deve ser passado como carimbos de data/hora do UNIX. |
| **NBF** (opcional) |Indica o horário em que o token começa a ser válido. O valor deve ser passado como carimbos de data/hora do UNIX. |

Um token de aplicativo de exemplo é semelhante A:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Quando decodificado, ele é semelhante a:

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

Há métodos disponíveis nos SDKs que facilitam a criação de tokens de aplicativo. Por exemplo, para .NET, você pode examinar a classe [Microsoft. PowerBI. Security. PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) e os métodos [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) .

Para o SDK do .NET, você pode se referir a [escopos](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Âmbitos

Ao usar tokens de inserção, talvez você queira restringir o uso dos recursos aos quais você concede acesso. Por esse motivo, você pode gerar um token com permissões no escopo.

A seguir estão os escopos disponíveis para Power BI coleções de espaço de trabalho.

|Âmbito|Descrição|
|---|---|
|Dataset.Read|Fornece permissão para ler o conjunto de um especificado.|
|Dataset.Write|Fornece permissão para gravar no conjunto de um especificado.|
|Dataset.ReadWrite|Fornece permissão para ler e gravar no DataSet especificado.|
|Report.Read|Fornece permissão para exibir o relatório especificado.|
|Report.ReadWrite|Fornece permissão para exibir e editar o relatório especificado.|
|Workspace.Report.Create|Fornece permissão para criar um novo relatório dentro do espaço de trabalho especificado.|
|Workspace. Report. Copy|Fornece permissão para clonar um relatório existente no espaço de trabalho especificado.|

Você pode fornecer vários escopos usando um espaço entre os escopos como o seguinte.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Declarações necessárias-escopos**

SCP: {scopesClaim} scopesClaim pode ser uma cadeia de caracteres ou uma matriz de cadeias, observando as permissões permitidas para recursos do espaço de trabalho (relatório, conjunto de recursos, etc.)

Um token decodificado, com escopos definidos, seria semelhante a:

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

### <a name="operations-and-scopes"></a>Operações e escopos

|Operação|Recurso de destino|Permissões de token|
|---|---|---|
|Crie (na memória) um novo relatório com base em um conjunto de um.|Conjunto de dados|Dataset.Read|
|Crie (na memória) um novo relatório com base em um conjunto de um e salve o relatório.|Conjunto de dados|* DataSet. Read<br>* Workspace. Report. Create|
|Exibir e explorar/editar (na memória) um relatório existente. Report. Read implica DataSet. Read. Report. Read não permite salvar edições.|Relatório|Report.Read|
|Editar e salvar um relatório existente.|Relatório|Report.ReadWrite|
|Salvar uma cópia de um relatório (salvar como).|Relatório|* Report. Read<br>* Workspace. Report. Copy|

## <a name="heres-how-the-flow-works"></a>Veja como funciona o fluxo
1. Copie as chaves de API para seu aplicativo. Você pode obter as chaves em **portal do Azure**.
   
    ![Onde encontrar as chaves de API no portal do Azure](media/get-started-sample/azure-portal.png)
1. O token declara uma declaração e tem um tempo de expiração.
   
    ![Fluxo do token de aplicativo-declaração de declarações de token](media/get-started-sample/token-2.png)
1. O token é assinado com chaves de acesso de API.
   
    ![Fluxo do token de aplicativo-o token é assinado](media/get-started-sample/token-3.png)
1. Solicitações do usuário para exibir um relatório.
   
    ![Fluxo do token de aplicativo – solicitações do usuário para exibir um relatório](media/get-started-sample/token-4.png)
1. O token é validado com chaves de acesso à API.
   
   ![Fluxo do token de aplicativo-o token é validado](media/get-started-sample/token-5.png)
1. Power BI coleções de espaços de trabalho enviam um relatório ao usuário.
   
   ![Fluxo do token de aplicativo-serviço Enviar relatório para o usuário](media/get-started-sample/token-6.png)

Depois de **Power bi as coleções de espaços de trabalho** enviam um relatório ao usuário, o usuário pode exibir o relatório em seu aplicativo personalizado. Por exemplo, se você importou o [exemplo de PBIX analisando dados de vendas](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), o aplicativo Web de exemplo ficaria assim:

![Exemplo de relatório inserido no aplicativo](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Veja também

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Introdução ao exemplo de coleções de espaços de trabalho do Microsoft Power BI](get-started-sample.md)  
[Cenários comuns de coleções de espaços de trabalho do Microsoft Power BI](scenarios.md)  
[Introdução às coleções de espaços de trabalho do Microsoft Power BI](get-started.md)  
[Repositório de git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
