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
ms.date: 03/26/2021
ms.openlocfilehash: 59847f745037acec47415489cdf61d119a7807af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936279"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Acesso Azure API para FHIR com Carteiro

Uma aplicação ao cliente pode aceder à AZure API para FHIR através de uma [API REST](https://www.hl7.org/fhir/http.html). Para enviar pedidos, ver respostas e depurar a sua aplicação à medida que está a ser construída, utilize uma ferramenta de teste API à sua escolha. Neste tutorial, vamos acompanhá-lo através dos passos de acesso ao servidor FHIR usando [o Carteiro.](https://www.getpostman.com/) 

## <a name="prerequisites"></a>Pré-requisitos

- Um ponto final da FHIR em Azure. 

  Para implantar a Azure API para FHIR (um serviço gerido), pode utilizar o [portal Azure](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)ou [Azure CLI](fhir-paas-cli-quickstart.md).

- Um [pedido de cliente confidencial](register-confidential-azure-ad-client-app.md) registrado para aceder ao serviço FHIR.
- Concedeu permissões à aplicação confidencial do cliente, por exemplo, "FHIR Data Contributor", para aceder ao serviço FHIR. Para mais informações, consulte [Configure Azure RBAC para FHIR](./configure-azure-rbac.md).
- Carteiro instalado. 
    
  Para mais informações sobre o Carteiro, consulte [Get Started with Postman](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>Servidor FHIR e detalhes de autenticação

Para utilizar o Carteiro, são necessários os seguintes parâmetros de autenticação:

- O URL do seu servidor FHIR, por exemplo, `https://MYACCOUNT.azurehealthcareapis.com`

- O fornecedor de identidade `Authority` do seu servidor FHIR, por exemplo, `https://login.microsoftonline.com/{TENANT-ID}`

- O configurado `audience` que normalmente é o URL do servidor FHIR, por exemplo, `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` ou `https://azurehealthcareapis.com` .

- O `client_id` ID ou aplicação da [aplicação confidencial](register-confidential-azure-ad-client-app.md) do cliente utilizada para aceder ao serviço FHIR.

- O `client_secret` segredo ou aplicação da aplicação confidencial do cliente.

Finalmente, deve verificar se `https://www.getpostman.com/oauth2/callback` se trata de um URL de resposta registado para a sua aplicação ao cliente.

## <a name="connect-to-fhir-server"></a>Ligue-se ao servidor FHIR

Abra o Carteiro e, em seguida, selecione **GET** para fazer um pedido para `https://fhir-server-url/metadata` .

![Declaração de Capacidade de Metadados do Carteiro](media/tutorial-postman/postman-metadata.png)

O URL de metadados para AZure API para FHIR é `https://MYACCOUNT.azurehealthcareapis.com/metadata` . 

Neste exemplo, o URL do servidor FHIR é `https://fhirdocsmsft.azurewebsites.net` , e a declaração de capacidade do servidor está disponível em `https://fhirdocsmsft.azurewebsites.net/metadata` . Este ponto final é acessível sem autenticação.

Se tentar aceder a recursos restritos, ocorre uma resposta "Autenticação falhada".

![Autenticação falhada](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Obtenção de um token de acesso
Selecione **Obter Novo Token de Acesso**.

Para obter um token de acesso válido, selecione **Autorização** e selecione **OAuth 2.0** do menu **TYPE** drop-down.

![Definir OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Selecione **Obter Novo Token de Acesso**.

![Solicitar novo token de acesso](media/tutorial-postman/postman-request-token.png)

Na caixa de diálogo **Get New Access Token,** insira os seguintes detalhes:

| Campo                 | Valor de Exemplo                                                                                                   | Comentário                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Nome do Token            | MYTOKEN                                                                                                         | Um nome que escolher          |
| Tipo de Concessão            | Código de Autorização                                                                                              |                            |
| URL de chamada de retorno          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| URL Auth              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` é `https://MYACCOUNT.azurehealthcareapis.com` para Azure API para FHIR |
| URL do Token de Acesso      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| ID de Cliente             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | ID da Aplicação             |
| Segredo do Cliente         | `XXXXXXXX`                                                                                                      | Chave de cliente secreto          |
| Âmbito | `<Leave Blank>` | O âmbito não é utilizado; portanto, pode ser deixado em branco.  
| Estado                 | `1234`     | [O Estado](https://learning.postman.com/docs/sending-requests/authorization/) é um valor opaco para evitar a falsificação de pedidos transversais. É opcional e pode ter um valor arbitrário como '1234'.                           |
| Autenticação de Cliente | Enviar credenciais de cliente no corpo                                                                                 |                 

Selecione **Request Token** para ser guiado através do fluxo de autenticação do Diretório Ativo Azure, e um token será devolvido ao Carteiro. Se ocorrer uma falha de autenticação, consulte a Consola do Carteiro para obter mais detalhes. **Nota:** Na fita, selecione **Ver** e, em seguida, selecione **Show Postman Console**. O atalho do teclado para a Consola do Carteiro é **Alt-Ctrl+C**.

Desloque-se para baixo para ver o ecrã de token devolvido e, em seguida, selecione **Use Token**.

![Use Token](media/tutorial-postman/postman-use-token.png)

Consulte o campo **Access Token** para ver o token recém-povoado. Se selecionar **Enviar** para repetir a `Patient` procura de recursos, um **Estado** `200 OK` é devolvido. Um estado de retorno `200 OK` indica um pedido HTTP bem sucedido.

![200 OK](media/tutorial-postman/postman-200-OK.png)

No exemplo de pesquisa do *Paciente,* não há pacientes na base de dados de tal forma que o resultado da pesquisa esteja vazio.

Pode inspecionar o token de acesso utilizando uma ferramenta como [jwt.ms](https://jwt.ms). Um exemplo do conteúdo é mostrado abaixo.

```json
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

Em situações de resolução de problemas, validar que tem o público correto `aud` (reivindicação) é um bom lugar para começar. Se o seu token for do emitente correto `iss` (reclamação) e tiver o público correto `aud` (reivindicação), mas ainda não conseguir aceder à API FHIR, é provável que o utilizador ou o principal de serviço `oid` (reclamação) não tenham acesso ao plano de dados FHIR. Recomendamos que utilize o [controlo de acesso baseado em funções (Azure RBAC)](configure-azure-rbac.md) para atribuir funções de plano de dados aos utilizadores. Se estiver a usar um inquilino de diretório azure ative externo e secundário para o seu plano de dados, terá de [configurar o RBAC local para](configure-local-rbac.md) as missões FHIR.

Também é possível obter um símbolo para a [AZure API para fHIR usando o Azure CLI](get-healthcare-apis-access-token-cli.md). Se estiver a utilizar um símbolo obtido com o CLI Azure, deverá utilizar o *Token do* tipo de Autorização . Cole o símbolo diretamente.

## <a name="inserting-a-patient"></a>Inserção de um paciente

Com um token de acesso válido, pode agora inserir um novo paciente. No Carteiro, altere o método selecionando **Post** e adicione o seguinte documento JSON no corpo do pedido.

[!code-json[](../samples/sample-patient.json)]

Selecione **Enviar** para determinar se o paciente foi criado com sucesso.

![Screenshot que mostra que o paciente é criado com sucesso.](media/tutorial-postman/postman-patient-created.png)

Se repetir a procura do paciente, deve agora ver o registo do paciente.

![Paciente criado](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, acedeu à Azure API para fHIR usando o Carteiro. Para obter mais informações sobre a Azure API para funcionalidades FHIR, consulte
 
>[!div class="nextstepaction"]
>[Funcionalidades suportadas](fhir-features-supported.md)
