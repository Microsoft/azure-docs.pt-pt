---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081537"
---
#### <a name="no-response-from-the-backend-service"></a>Nenhuma resposta do serviço de backend

Ao testar localmente, certifique-se de que o serviço de backend está em funcionamento e está a utilizar a porta correta.

Se testar contra a **App AZure API,** verifique se o serviço está em execução e foi implantado e começou sem erros.

Certifique-se de verificar se especificou o endereço base corretamente no **[Carteiro](https://www.postman.com/downloads)** ou na configuração da aplicação móvel ao testar através do cliente. O endereço base deve ser indicativo `https://<api_name>.azurewebsites.net/` ou `https://localhost:5001/` ao testar localmente.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Não receber notificações no Android depois de iniciar ou parar uma sessão de depuragem

Certifique-se de que se regista novamente depois de iniciar ou parar uma sessão de depuragem. O depurador fará com que um novo **símbolo da base** de fogo seja gerado. A instalação do centro de notificação também deve ser atualizada.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Receber um código de estado 401 do serviço backend

Valide que está a configurar o cabeçalho de pedido **apikey** e este valor corresponde ao que tinha configurado para o serviço de backend.

Se receber este erro ao testar localmente, certifique-se de que o valor chave definido na config cliente corresponde ao valor de configuração do utilizador **Autenticação:ApiKey** utilizado pela [API](#create-the-api-app).

Se estiver a testar com uma **App API**, certifique-se de que o valor chave no ficheiro conge do cliente corresponde à **definição de aplicação Autenticação:ApiKey** que está a utilizar na [App API](#create-the-api-app).

> [!NOTE]
> Se tiver criado ou alterado esta definição depois de ter implantado o serviço de backend, então deve reiniciar o serviço para que este entre em vigor.

Se optar por não completar os [clientes Authenticate utilizando uma secção chave API,](#authenticate-clients-using-an-api-key-optional) certifique-se de que não aplicou o atributo **Authorize** à classe **NotificationsController.**

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Receber um código de estado 404 do serviço backend

Validar que o método de pedido de ponto final e HTTP está correto. Por exemplo, os pontos finais devem ser indicativamente:

- **[PUT]**`https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[EXCLUIR]**`https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]**`https://<api_name>.azurewebsites.net/api/notifications/requests`

Ou ao testar localmente:

- **[PUT]**`https://localhost:5001/api/notifications/installations`
- **[EXCLUIR]**`https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]**`https://localhost:5001/api/notifications/requests`

Ao especificar o endereço base na aplicação do cliente, certifique-se de que termina com um `/` . O endereço base deve ser indicativo `https://<api_name>.azurewebsites.net/` ou `https://localhost:5001/` ao testar localmente.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Não é possível registar-se e é apresentada uma mensagem de erro do centro de notificação

Verifique se o dispositivo de teste tem conectividade de rede. Em seguida, determine o código de estado de resposta http, definindo um ponto de rutura para inspecionar o valor da propriedade **StatusCode** no **HttpResponse**.

Reveja as sugestões anteriores de resolução de problemas, quando aplicável com base no código de estado.

Desaponte um ponto de rutura nas linhas que devolvem estes códigos de estado específicos para a respetiva API. Em seguida, tente ligar para o serviço de backend quando depurar localmente.

Validar o serviço de backend está a funcionar como esperado através **[do Carteiro](https://www.postman.com/downloads)** utilizando a carga útil adequada. Utilize a carga útil real criada pelo código do cliente para a plataforma em questão.

Reveja as secções de configuração específicas da plataforma para garantir que não foram perdidos passos. Verifique se os valores adequados estão a ser `installation id` resolvidos e `token` as variáveis para a plataforma apropriada.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Não é possível resolver um ID para a mensagem de erro do dispositivo é apresentado

Reveja as secções de configuração específicas da plataforma para garantir que não foram perdidos passos.
