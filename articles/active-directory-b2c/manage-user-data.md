---
title: Gerir os dados dos utilizadores no Diretório Ativo Azure B2C  Microsoft Docs
description: Saiba como eliminar ou exportar dados de utilizadores em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e245b58449ab773914fc60be056082b82f05035a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184490"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gerir os dados dos utilizadores no Diretório Ativo Azure B2C

 Este artigo discute como pode gerir os dados dos utilizadores no Azure Ative Directory B2C (Azure AD B2C) utilizando as operações fornecidas pela [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api). A gestão dos dados dos utilizadores inclui a desposição ou a exportação de dados a partir de registos de auditoria.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Eliminar dados dos utilizadores

Os dados dos utilizadores são armazenados no diretório Azure AD B2C e nos registos de auditoria. Todos os dados de auditoria dos utilizadores são conservados durante 7 dias no Azure AD B2C. Se pretender eliminar os dados do utilizador dentro desse período de 7 dias, pode utilizar a operação eliminar uma operação do [utilizador.](https://docs.microsoft.com/graph/api/user-delete) É necessária uma operação DELETE para cada um dos inquilinos Do Azure AD B2C onde os dados possam residir.

A cada utilizador do Azure AD B2C é atribuído um ID de objeto. O ID do objeto fornece um identificador inequívoco para que possa utilizar para eliminar os dados do utilizador no Azure AD B2C. Dependendo da sua arquitetura, o ID do objeto pode ser um identificador de correlação útil em outros serviços, tais como bases de dados de gestão de relacionamento financeiro, marketing e relacionamento com o cliente.

A forma mais precisa de obter o ID do objeto para um utilizador é obtê-lo como parte de uma viagem de autenticação com O Azure AD B2C. Se receber um pedido válido de dados de um utilizador utilizando outros métodos, um processo offline, como uma pesquisa por um agente de suporte ao serviço do cliente, pode ser necessário para encontrar o utilizador e observar o ID do objeto associado.

O exemplo que se segue mostra um possível fluxo de eliminação de dados:

1. O utilizador entra e seleciona **Apagar os meus dados**.
2. A aplicação oferece uma opção para apagar os dados dentro de uma secção de administração da aplicação.
3. A aplicação obriga a uma autenticação ao Azure AD B2C. O Azure AD B2C fornece um símbolo com o ID do objeto do utilizador de volta à aplicação.
4. O símbolo é recebido pela aplicação e o ID do objeto é usado para eliminar os dados do utilizador através de uma chamada para o Microsoft Graph API. O Microsoft Graph API elimina os dados do utilizador e devolve um código de estado de 200 OK.
5. A aplicação orquestra a eliminação de dados dos utilizadores noutros sistemas organizacionais, conforme necessário, utilizando o ID do objeto ou outros identificadores.
6. A aplicação confirma a eliminação de dados e fornece os próximos passos ao utilizador.

## <a name="export-customer-data"></a>Exportar dados dos clientes

O processo de exportação de dados de clientes do Azure AD B2C é semelhante ao processo de eliminação.

Os dados dos utilizadores do Azure AD B2C limitam-se a:

- **Dados armazenados no Diretório Ativo Do Azure**: Pode recuperar dados numa viagem de utilizador de autenticação Azure AD B2C utilizando o ID do objeto ou qualquer nome de entrada, como um endereço de e-mail ou nome de utilizador.
- **Relatório de eventos de auditoria específicos**do utilizador : Pode indexar dados utilizando o ID do objeto.

No exemplo seguinte de um fluxo de dados de exportação, as etapas descritas como sendo executadas pela aplicação também podem ser executadas por um processo de backend ou um utilizador com uma função de administrador no diretório:

1. O utilizador insere-se na aplicação. O Azure AD B2C impõe a autenticação com a autenticação azure multi-factor, se necessário.
2. A aplicação utiliza as credenciais do utilizador para ligar para uma operação Microsoft Graph API para recuperar os atributos do utilizador. O Microsoft Graph API fornece os dados de atributos no formato JSON. Dependendo do esquema, pode definir o conteúdo do token ID para incluir todos os dados pessoais sobre um utilizador.
3. A aplicação recupera a atividade de auditoria do utilizador. A Microsoft Graph API fornece os dados do evento à aplicação.
4. A aplicação agrega os dados e disponibiliza-os ao utilizador.

## <a name="next-steps"></a>Passos seguintes

Para saber como gerir a forma como os utilizadores acedem à sua aplicação, consulte [Gerir o acesso ao utilizador.](manage-user-access.md)
