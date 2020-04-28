---
title: Erro inesperado ao executar o consentimento de uma aplicação Microsoft Docs
description: Discute erros que podem ocorrer durante o processo de consentir com uma aplicação e o que pode fazer sobre eles
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea14e02920cf7ba6c5e0a7b415cb92137c915576
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519709"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Erro inesperado ao executar o consentimento de uma aplicação

Este artigo discute erros que podem ocorrer durante o processo de consentir com uma aplicação. Se estiver a resolver avisos de consentimento inesperados que não contenham mensagens de erro, consulte Cenários de [Autenticação para AD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Muitas aplicações que se integram com o Diretório Ativo azure requerem permissões para aceder a outros recursos para funcionar. Quando estes recursos também são integrados com o Azure Ative Directory, as permissões de acesso aos mesmos são frequentemente solicitadas utilizando o quadro comum de consentimento. É apresentado um pedido de consentimento, que geralmente ocorre na primeira vez que uma aplicação é usada, mas também pode ocorrer numa utilização subsequente da aplicação.

Certas condições devem ser verdadeiras para que um utilizador consinta nas permissões que uma aplicação necessita. Se estas condições não forem satisfeitas, podem ocorrer os seguintes erros.

## <a name="requesting-not-authorized-permissions-error"></a>Solicitando erro de permissões não autorizadas
* **AADSTS90093:** &lt;o clienteAppDisplayName&gt; está a solicitar uma ou mais permissões que não está autorizado a conceder. Contacte um administrador, que possa consentir com este pedido em seu nome.
* **AADSTS90094:** &lt;o clienteAppDisplayName&gt; precisa de autorização para aceder a recursos na sua organização que apenas um administrador pode conceder. Peça a um administrador para conceder permissão para esta aplicação antes de poder utilizá-la.

Este erro ocorre quando um utilizador que não é administrador da empresa tenta utilizar uma aplicação que está a solicitar permissões que apenas um administrador pode conceder. Este erro pode ser resolvido por um administrador que concede acesso ao pedido em nome da sua organização.

Este erro também pode ocorrer quando um utilizador é impedido de consentir com uma aplicação devido à Microsoft detetar que o pedido de permissões é arriscado. Neste caso, será também registado um evento de auditoria com uma categoria de "Gestão de Aplicações", Tipo de Atividade de "Consentimento para aplicação" e Motivo de Estado da "aplicação arriscada detetada".

## <a name="policy-prevents-granting-permissions-error"></a>Política impede concessão de erros de permissões
* **AADSTS90093:** Um administrador &lt;do&gt; tenantDisplayName definiu uma política &lt;que o&gt; impede de conceder o nome da app as permissões que está a solicitar. Contacte um &lt;administrador&gt;do tenantDisplayName, que pode conceder permissões a esta aplicação em seu nome.

Este erro ocorre quando um administrador da empresa desliga a capacidade de os utilizadores consentirem com as aplicações, e ntão um utilizador não administrador tenta usar uma aplicação que requer consentimento. Este erro pode ser resolvido por um administrador que concede acesso ao pedido em nome da sua organização.

## <a name="intermittent-problem-error"></a>Erro de problema intermitente
* **AADSTS90090:** Parece que o processo de login encontrou um problema intermitente a &lt;registar as&gt;permissões que tentou conceder ao clienteAppDisplayName . tentar novamente mais tarde.

Este erro indica que ocorreu um problema intermitente do lado do serviço. Pode ser resolvido tentando consentir o pedido novamente.

## <a name="resource-not-available-error"></a>Erro não disponível de recurso
* **AADS65005:** O &lt;cliente da&gt; aplicaçãoAppDisplayName solicitou &lt;permissões&gt; para aceder a um recurso AppDisplayName que não está disponível. 

Contacte o programador da aplicação.

##  <a name="resource-not-available-in-tenant-error"></a>Recurso não disponível em erro de inquilino
* **AADSTS65005:** &lt;&gt; clientAppDisplayName está a solicitar &lt;acesso a&gt; um recurso de &lt;recursoAppDisplayName que não está disponível na sua organização inquilinoDisplayName&gt;. 

Certifique-se de que este recurso &lt;está&gt;disponível ou contacte um administrador do tenantDisplayName .

## <a name="permissions-mismatch-error"></a>Permissões erro de desajuste
* **AADS65005:** A aplicação solicitou consentimento &lt;para aceder&gt;ao recurso AppDisplayName . Este pedido falhou porque não corresponde à forma como a aplicação foi pré-configurada durante o registo da aplicação. Contacte o fornecedor de aplicações.**

Todos estes erros ocorrem quando a aplicação a que um utilizador está a tentar consentir está a solicitar permissões para aceder a uma aplicação de recursos que não pode ser encontrada no diretório da organização (inquilino). Esta situação pode ocorrer por várias razões:

-   O desenvolvedor de aplicações do cliente configurou a sua aplicação de forma incorreta, fazendo com que solicitasse acesso a um recurso inválido. Neste caso, o desenvolvedor de aplicações deve atualizar a configuração da aplicação do cliente para resolver este problema.

-   Um Diretor de Serviço que representa a aplicação de recursos-alvo não existe na organização, nem existiu no passado, mas foi removido. Para resolver este problema, um Diretor de Serviço para a aplicação de recursos deve ser aprovisionado na organização para que a aplicação do cliente possa solicitar permissões para o mesmo. O Diretor de Serviço pode ser provisionado de várias formas, dependendo do tipo de aplicação, incluindo:

    -   Adquirir uma subscrição para a aplicação de recursos (aplicações publicadas pela Microsoft)

    -   Consentimento para a aplicação de recursos

    -   Concessão das permissões de pedido através do portal Azure

    -   Adicionar a aplicação da Galeria de Aplicação Da AD Azure

## <a name="next-steps"></a>Passos seguintes 

[Apps, permissões e consentimento no Diretório Ativo azure (v1 endpoint)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Âmbitos, permissões e consentimento no Diretório Ativo Azure (v2.0 endpoint)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


