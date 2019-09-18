---
title: Gerenciar dados do usuário no Azure Active Directory B2C | Microsoft Docs
description: Saiba como excluir ou exportar dados do usuário no Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 78726620db119abf617be8a30cf03697b04e382b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064071"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gerenciar dados do usuário no Azure Active Directory B2C

 Este artigo discute como você pode gerenciar os dados do usuário em Azure Active Directory B2C (Azure AD B2C) usando as operações fornecidas pelo [API do Graph de Azure Active Directory](/previous-versions/azure/ad/graph/api/api-catalog). O gerenciamento de dados do usuário inclui a exclusão ou a exportação de dados de logs de auditoria.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Excluir dados do usuário

Os dados do usuário são armazenados no diretório Azure AD B2C e nos logs de auditoria. Todos os dados de auditoria de usuário são mantidos por 7 dias em Azure AD B2C. Se você quiser excluir os dados do usuário dentro desse período de 7 dias, poderá usar a operação [excluir um usuário](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) . Uma operação de exclusão é necessária para cada um dos locatários Azure AD B2C onde os dados podem residir.

Cada usuário no Azure AD B2C é atribuído a uma ID de objeto. A ID de objeto fornece um identificador não ambíguo para você usar para excluir dados do usuário em Azure AD B2C. Dependendo de sua arquitetura, a ID de objeto pode ser um identificador de correlação útil entre outros serviços, como bancos de dados de gerenciamento de relacionamento financeiro, de marketing e de clientes.

A maneira mais precisa de obter a ID de objeto para um usuário é obtê-lo como parte de uma jornada de autenticação com Azure AD B2C. Se você receber uma solicitação válida de dados de um usuário usando outros métodos, um processo offline, como uma pesquisa por um agente de suporte do atendimento ao cliente, poderá ser necessário para localizar o usuário e anotar a ID do objeto associado.

O exemplo a seguir mostra um possível fluxo de exclusão de dados:

1. O usuário entra e seleciona **Excluir meus dados**.
2. O aplicativo oferece uma opção para excluir os dados em uma seção de administração do aplicativo.
3. O aplicativo força uma autenticação a Azure AD B2C. Azure AD B2C fornece um token com a ID de objeto do usuário de volta para o aplicativo.
4. O token é recebido pelo aplicativo e a ID do objeto é usada para excluir os dados do usuário por meio de uma chamada para o API do Graph do Azure AD. O API do Graph do Azure AD exclui os dados do usuário e retorna um código de status de 200 OK.
5. O aplicativo orquestra a exclusão de dados do usuário em outros sistemas organizacionais, conforme necessário, usando a ID de objeto ou outros identificadores.
6. O aplicativo confirma a exclusão de dados e fornece as próximas etapas para o usuário.

## <a name="export-customer-data"></a>Exportar dados do cliente

O processo de exportação de dados do cliente do Azure AD B2C é semelhante ao processo de exclusão.

Azure AD B2C dados do usuário são limitados a:

- **Dados armazenados no Azure Active Directory**: Você pode recuperar dados em um percurso de usuário de autenticação de Azure AD B2C usando a ID de objeto ou qualquer nome de entrada, como um endereço de email ou de nome de usuário.
- **Relatório de eventos de auditoria específicos do usuário**: Você pode indexar dados usando a ID de objeto.

No exemplo a seguir de um fluxo de dados de exportação, as etapas descritas como sendo executadas pelo aplicativo também podem ser executadas por um processo de back-end ou um usuário com uma função de administrador no diretório:

1. O usuário entra no aplicativo. Azure AD B2C impõe a autenticação com a autenticação multifator do Azure, se necessário.
2. O aplicativo usa as credenciais do usuário para chamar uma operação de API do Graph do Azure AD para recuperar os atributos de usuário. O API do Graph do Azure AD fornece os dados de atributo no formato JSON. Dependendo do esquema, você pode definir o conteúdo do token de ID para incluir todos os dados pessoais de um usuário.
3. O aplicativo recupera a atividade de auditoria do usuário. O API do Graph do Azure AD fornece os dados de evento para o aplicativo.
4. O aplicativo agrega os dados e os disponibiliza para o usuário.

## <a name="next-steps"></a>Passos Seguintes

- Para saber como gerenciar como os usuários acessam seu aplicativo, consulte [gerenciar o acesso do usuário](manage-user-access.md).




