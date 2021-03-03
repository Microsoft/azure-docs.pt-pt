---
title: Carteiro FHIR servidor em Azure - Azure API para FHIR
description: Neste tutorial, vamos percorrer os passos necessários para usar o Carteiro para aceder a um servidor FHIR. O carteiro é útil para depurar aplicações que acedam a APIs.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/01/2021
ms.openlocfilehash: 6c4d63862f4e02f3fd525021921b02cde6d10f61
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699339"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Acesso Azure API para FHIR com Carteiro

Uma aplicação de cliente acederia a uma API FHIR através de uma [API REST](https://www.hl7.org/fhir/http.html). Também pode querer interagir diretamente com o servidor FHIR à medida que constrói aplicações, por exemplo, para fins de depurar. Neste tutorial, vamos percorrer os passos necessários para usar o [Carteiro](https://www.getpostman.com/) para aceder a um servidor FHIR. O Carteiro é uma ferramenta frequentemente usada para depurar quando constrói aplicações que acedem a APIs.

## <a name="prerequisites"></a>Pré-requisitos

- Um ponto final da FHIR em Azure. Pode configurar isso utilizando o Azure API gerido para FHIR ou o servidor FHIR open source para o Azure. Configurar a Azure API gerida para fHIR utilizando [o portal Azure](fhir-paas-portal-quickstart.md), [PowerShell,](fhir-paas-powershell-quickstart.md)ou [Azure CLI](fhir-paas-cli-quickstart.md).
- Uma  [aplicação ao cliente](register-confidential-azure-ad-client-app.md) que irá utilizar para aceder ao serviço FHIR.
- Concedeu permissões, por exemplo, "FHIR Data Contributor", à aplicação do cliente para aceder ao serviço FHIR. Mais informações na [Configure Azure RBAC para FHIR](./configure-azure-rbac.md)
- Carteiro instalado. Pode obtê-lo de [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>Servidor FHIR e detalhes de autenticação

Para utilizar o Carteiro, são necessários os seguintes detalhes:

- O URL do seu servidor FHIR, por exemplo `https://MYACCOUNT.azurehealthcareapis.com`
- O fornecedor de identidade `Authority` do seu servidor FHIR, por exemplo, `https://login.microsoftonline.com/{TENANT-ID}`
- Os `audience` configurados. Este é geralmente o URL do servidor FHIR, por exemplo `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` ou apenas `https://azurehealthcareapis.com` .
- O `client_id` (ou ID de aplicação) da [aplicação](register-confidential-azure-ad-client-app.md) do cliente que você vai usar para aceder ao serviço FHIR.
- O `client_secret` (ou segredo de aplicação) da aplicação do cliente.

Finalmente, deve verificar se `https://www.getpostman.com/oauth2/callback` se trata de um URL de resposta registado para a sua aplicação ao cliente.

## <a name="connect-to-fhir-server"></a>Ligue-se ao servidor FHIR

Usando o Carteiro, faça um `GET` pedido `https://fhir-server-url/metadata` para:

![Declaração de Capacidade de Metadados do Carteiro](media/tutorial-postman/postman-metadata.png)

O URL de metadados para AZure API para FHIR é `https://MYACCOUNT.azurehealthcareapis.com/metadata` . Neste exemplo, o URL do servidor FHIR é `https://fhirdocsmsft.azurewebsites.net` e a declaração de capacidade do servidor está disponível em `https://fhirdocsmsft.azurewebsites.net/metadata` . Este ponto final deve ser acessível sem autenticação.

Se tentar aceder a recursos restritos, deverá obter uma resposta "Autenticação falhada":

![Autenticação falhada](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Obtenção de um token de acesso

Para obter um token de acesso válido, selecione "Autorização" e escolha TIPO "OAuth 2.0":

![Definir OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Hit "Get New Access Token" e aparece um diálogo:

![Solicitar novo token de acesso](media/tutorial-postman/postman-request-token.png)

Precisará de alguns detalhes:

| Campo                 | Valor de Exemplo                                                                                                   | Comentário                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Nome do Token            | MYTOKEN                                                                                                         | Um nome que escolher          |
| Tipo de Concessão            | Código de Autorização                                                                                              |                            |
| URL de chamada de retorno          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| URL Auth              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` é `https://MYACCOUNT.azurehealthcareapis.com` para Azure API para FHIR |
| URL do Token de Acesso      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| ID de Cliente             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | ID da Aplicação             |
| Segredo do Cliente         | `XXXXXXXX`                                                                                                        | Chave de cliente secreto          |
| Âmbito | `<Leave Blank>` |
| Estado                 | `1234`                                                                                                            |                            |
| Autenticação de Cliente | Enviar credenciais de cliente no corpo                                                                                 |                 

Bata em "Request Token" e será guiado através do fluxo de autenticação do Diretório Ativo Azure e um token será devolvido ao Carteiro. Se tiver problemas, abra a Consola do Carteiro (a partir do item do menu "View->Show Postman Console").

Desloque-se para baixo no ecrã de token devolvido e acerte em "Use Token":

![Use Token](media/tutorial-postman/postman-use-token.png)

O token deve agora ser povoado no campo "Access Token" e pode selecionar fichas de "Tokens disponíveis". Se "Enviar" novamente para repetir a `Patient` pesquisa de recursos, deve obter um Estado `200 OK` :

![200 OK](media/tutorial-postman/postman-200-OK.png)

Neste caso, não há pacientes na base de dados e a procura está vazia.

Se inspecionar o token de acesso com uma ferramenta [https://jwt.ms](https://jwt.ms) como, deve ver conteúdos como:

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Em situações de resolução de problemas, validar que tem o público correto `aud` (reivindicação) é um bom lugar para começar. Se o seu token for do emitente correto `iss` (reclamação) e tiver o público correto `aud` (reivindicação), mas ainda não conseguir aceder à API FHIR, é provável que o utilizador ou o principal de serviço `oid` (reclamação) não tenham acesso ao plano de dados FHIR. Recomendamos que utilize o [controlo de acesso baseado em funções (Azure RBAC)](configure-azure-rbac.md) para atribuir funções de plano de dados aos utilizadores. Se estiver a utilizar um inquilino de diretório Azure Ative externo e secundário para o seu plano de dados, terá de [configurar as atribuições locais do RBAC](configure-local-rbac.md).

Também é possível [obter um símbolo para a AZure API para fHIR usando o Azure CLI](get-healthcare-apis-access-token-cli.md). Se estiver a utilizar um símbolo obtido com o CLI Azure, deverá utilizar o tipo de Autorização "Token portador" e colar o token diretamente.

## <a name="inserting-a-patient"></a>Inserção de um paciente

Agora que tem um sinal de acesso válido. Pode inserir um novo paciente. Mude para o método "POST" e adicione o seguinte documento JSON no corpo do pedido:

[!code-json[](samples/sample-patient.json)]

Bata em "Enviar" e deve ver se o paciente foi criado com sucesso:

![Screenshot que mostra que o paciente é criado com sucesso.](media/tutorial-postman/postman-patient-created.png)

Se repetir a procura do paciente, deve agora ver o registo do paciente:

![Paciente criado](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, acedeu a uma API FHIR usando carteiro. Leia sobre as funcionalidades de API suportadas na nossa secção de funcionalidades suportadas.
 
>[!div class="nextstepaction"]
>[Funcionalidades suportadas](fhir-features-supported.md)