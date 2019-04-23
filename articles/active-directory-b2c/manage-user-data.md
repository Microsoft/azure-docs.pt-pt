---
title: Gerir dados de utilizador no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como eliminar ou exportar dados de utilizador no Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 62846fe744e7295f58902481400ce91770c916da
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798114"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gerir dados de utilizador no Azure Active Directory B2C

 Este artigo descreve como pode gerir os dados de utilizador no Azure Active Directory (Azure AD) B2C usando as operações que são fornecidas pelos [do Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog). Gestão de dados de utilizador inclui a eliminar ou exportar dados de registos de auditoria.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Eliminar dados de utilizador

Dados de utilizador são armazenados no diretório do Azure AD B2C e nos registos de auditoria. Todos os dados de auditoria de utilizador são retidos durante 30 dias no Azure AD B2C. Se quiser eliminar dados de utilizador dentro desse período de 30 dias, pode utilizar o [eliminar um utilizador](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) operação. Uma operação de eliminação é necessária para cada um dos inquilinos do Azure AD B2C, onde os dados podem residir. 

Cada utilizador no Azure AD B2C está atribuído um ID de objeto. O ID de objeto fornece um identificador inequívoca a utilizar para eliminar dados de utilizador no Azure AD B2C. Dependendo da sua arquitetura, o ID de objeto pode ser um identificador de correlação útil em outros serviços, como financeiro, marketing e bases de dados de gestão de relação de cliente. 

É a forma mais exata para obter o ID de objeto de um utilizador para obtê-lo como parte de um percurso de autenticação com o Azure AD B2C. Se receber um pedido válido de dados de um utilizador ao utilizar outros métodos, um processo offline, por exemplo, uma pesquisa por um suporte de serviço ao cliente agente, poderá ser necessário para localizar o utilizador e anote o ID de objeto associado. 

O exemplo seguinte mostra um fluxo de eliminação de dados possíveis:

1. O utilizador inicia sessão e seleciona **eliminar dados do meu**.
2. O aplicativo oferece uma opção para eliminar os dados dentro de uma seção de administração do aplicativo.
3. O aplicativo força uma autenticação para o Azure AD B2C. O Azure AD B2C fornece um token com o ID de objeto do utilizador para a aplicação. 
4. O token é recebido pela aplicação e o objeto que ID é utilizado para eliminar os dados do utilizador através de uma chamada para o Azure AD Graph API. O Azure AD Graph API elimina os dados de utilizador e devolve um código de estado 200 OK.
5. O aplicativo orquestra a eliminação de dados de utilizador em outros sistemas organizacionais, conforme necessário com o ID de objeto ou outros identificadores.
6. A aplicação confirma que a eliminação de dados e fornece os passos seguintes para o usuário.

## <a name="export-customer-data"></a>Exportar dados do cliente

O processo de exportação de dados de cliente do Azure AD B2C é semelhante ao processo de eliminação.

Dados de utilizador do Azure AD B2C estão limitados a:

- **Dados armazenados no Azure Active Directory**: Pode recuperar dados num percurso de utilizador de autenticação do Azure AD B2C com o ID de objeto ou qualquer início de sessão no nome, tal como um endereço de e-mail ou nome de utilizador. 
- **Relatório de eventos de auditoria específicas do usuário**: Permite-lhe indexar dados com o ID de objeto.

No exemplo seguinte de um fluxo de dados de exportação, os passos que são descritos como, que está a ser executadas pela aplicação também podem ser executados por um processo de back-end ou um utilizador com uma função de administrador no diretório:

1. O utilizador inicia sessão na aplicação. O Azure AD B2C impõe a autenticação com o Azure multi-factor Authentication se for necessário.
2. O aplicativo utiliza as credenciais de utilizador para chamar uma operação do Azure AD Graph API para obter os atributos de utilizador. O Azure AD Graph API fornece os dados de atributo no formato JSON. Consoante o esquema, pode definir o conteúdo do token de ID para incluir todos os dados pessoais sobre um usuário.
3. A aplicação obtém a atividade de auditoria do utilizador. O Azure AD Graph API fornece os dados do evento para a aplicação.
4. O aplicativo agrega os dados e torna-o disponível para o usuário.

## <a name="next-steps"></a>Passos Seguintes

- Para saber como gerir a forma como os utilizadores aceder à sua aplicação, veja [gerir o acesso de utilizador](manage-user-access.md).




