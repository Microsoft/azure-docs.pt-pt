---
title: Gerir os dados dos utilizadores no Azure Ative Directory B2C ; Microsoft Docs
description: Saiba como eliminar ou exportar dados de utilizadores em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b7cc772e2a2e44a72af5e47a794c8b0f36aa9786
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387648"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gerir os dados dos utilizadores no Azure Ative Directory B2C

 Este artigo discute como pode gerir os dados do utilizador no Azure Ative Directory B2C (Azure AD B2C) utilizando as operações fornecidas pela [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api). A gestão dos dados dos utilizadores inclui a eliminação ou exportação de dados a partir de registos de auditoria.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Eliminar os dados dos utilizadores

Os dados do utilizador são armazenados no diretório Azure AD B2C e nos registos de auditoria. Todos os dados de auditoria do utilizador são retidos durante 7 dias em Azure AD B2C. Se pretender eliminar os dados do utilizador dentro desse período de 7 dias, pode utilizar a operação [de utilizador Eliminar.](https://docs.microsoft.com/graph/api/user-delete) É necessária uma operação DELETE para cada um dos inquilinos Azure AD B2C onde os dados possam residir.

Todos os utilizadores em Azure AD B2C são designados para um ID de objeto. O ID do objeto fornece um identificador inequívoco para que possa utilizar para eliminar os dados do utilizador em Azure AD B2C. Dependendo da sua arquitetura, o ID do objeto pode ser um identificador de correlação útil em outros serviços, tais como bases de dados de gestão de relacionamento com o cliente.

A forma mais precisa de obter o ID do objeto para um utilizador é obtê-lo como parte de uma viagem de autenticação com Azure AD B2C. Se receber um pedido válido de dados de um utilizador utilizando outros métodos, um processo offline, como uma pesquisa por um agente de apoio ao cliente, poderá ser necessário para encontrar o utilizador e anotar o ID do objeto associado.

O exemplo a seguir mostra um possível fluxo de eliminação de dados:

1. O utilizador inscreve-se e seleciona **Eliminar os meus dados.**
2. A aplicação oferece uma opção de apagar os dados dentro de uma secção de administração da aplicação.
3. A aplicação obriga a uma autenticação para Azure AD B2C. O Azure AD B2C fornece um símbolo com o iD do objeto do utilizador de volta à aplicação.
4. O token é recebido pela aplicação, e o ID do objeto é usado para eliminar os dados do utilizador através de uma chamada para a API do Gráfico microsoft. A API do Microsoft Graph elimina os dados do utilizador e devolve um código de estado de 200 OK.
5. A aplicação orquestra a eliminação de dados do utilizador em outros sistemas organizacionais, conforme necessário, utilizando o ID do objeto ou outros identificadores.
6. A aplicação confirma a eliminação de dados e fornece os próximos passos ao utilizador.

## <a name="export-customer-data"></a>Exportar dados de clientes

O processo de exportação de dados de clientes da Azure AD B2C é semelhante ao processo de eliminação.

Os dados dos utilizadores do Azure AD B2C limitam-se a:

- **Dados armazenados no Diretório Ativo Azure**: Pode obter dados numa viagem de utilizador de autenticação AZURE AD B2C utilizando o ID do objeto ou qualquer nome de inscrição, como um endereço de e-mail ou nome de utilizador.
- **Relatório de eventos de auditoria específicos do utilizador**: Pode indexar dados utilizando o ID do objeto.

No exemplo seguinte de um fluxo de dados de exportação, as etapas descritas como sendo executadas pela aplicação também podem ser executadas por um processo de backend ou por um utilizador com uma função de administrador no diretório:

1. O utilizador inscreve-se na aplicação. Azure AD B2C impõe a autenticação com autenticação multi-factor Azure, se necessário.
2. A aplicação utiliza as credenciais do utilizador para chamar uma operação API do Microsoft Graph para recuperar os atributos do utilizador. A API do Microsoft Graph fornece os dados do atributo no formato JSON. Dependendo do esquema, pode definir o conteúdo do token de ID para incluir todos os dados pessoais sobre um utilizador.
3. A aplicação recupera a atividade de auditoria do utilizador. A Microsoft Graph API fornece os dados do evento à aplicação.
4. A aplicação agrega os dados e coloca-os à disposição do utilizador.

## <a name="next-steps"></a>Passos seguintes

Para saber como gerir a forma como os utilizadores acedem à sua aplicação, consulte [Gerir o acesso ao utilizador.](manage-user-access.md)
