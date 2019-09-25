---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: c58977798fabc120f3a8647774f575bc32515dcd
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219043"
---
Aqui estão as restrições de utilização e outros limites de serviço do serviço do Azure Active Directory (Azure AD).

| Category | Limites |
| --- | --- |
| Diretórios | Um único utilizador pode pertencer a um máximo de 500 diretórios do Azure AD como membro ou convidado.<br/>Um único utilizador pode criar um máximo de 20 diretórios. |
| Domínios | Pode adicionar um máximo de 900 nomes de domínio gerido. Se configurar todos os seus domínios para federação com o Active Directory no local, pode adicionar um máximo de 450 nomes de domínio em cada diretório. |
|Recursos |<ul><li>Um máximo de 50.000 recursos do Azure AD pode ser criado em um único diretório por usuários da edição gratuita do Azure Active Directory por padrão. Se você tiver pelo menos um domínio verificado, a cota de serviço de diretório padrão no Azure AD será estendida para os recursos 300.000 do AD do Azure. </li><li>Um usuário não administrador pode criar no máximo 250 recursos do Azure AD. Recursos ativos e excluídos que estão disponíveis para restaurar a contagem em direção a essa cota. Somente os recursos do Azure AD excluídos que foram excluídos há menos de 30 dias estão disponíveis para restauração. Recursos do Azure AD excluídos que não estão mais disponíveis para restaurar a contagem em direção a essa cota em um valor de um trimestre por 30 dias. Se você tiver os desenvolvedores que provavelmente excederão repetidamente essa cota no decorrer de suas tarefas regulares, você poderá [criar e atribuir uma função personalizada](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) com permissão para criar um número ilimitado de registros de aplicativo.</li></ul> |
| Extensões de esquema |<ul><li>As extensões de tipo de cadeia podem ter até 256 carateres. </li><li>As extensões de tipo binário estão limitadas a 256 bytes.</li><li>Somente os valores de extensão 100, em *todos os* tipos e *todos os* aplicativos, podem ser gravados em qualquer recurso único do AD do Azure.</li><li>Apenas as entidades User, Group, TenantDetail, Device, Application e ServicePrincipal podem ser expandidas com os atributos de valor único do tipo de cadeia ou de tipo de binário.</li><li>As extensões de esquema estão disponíveis apenas na pré-visualização da versão 1.21 da Graph API. A aplicação tem de ter acesso de escrita para registar uma extensão.</li></ul> |
| Aplicações |No máximo, cem utilizadores podem ser proprietários de uma única aplicação. |
| Grupos |<ul><li>No máximo, cem utilizadores podem ser proprietários de um único grupo.</li><li>Qualquer número de recursos do AD do Azure pode ser membros de um único grupo.</li><li>Um utilizador pode ser membro de qualquer número de grupos.</li><li>O número de membros de um grupo que pode sincronizar a partir do Active Directory no local para o Azure Active Directory com o Azure AD Connect está limitado a 50 000 membros.</li></ul> |
| Proxy da aplicação | <ul><li>Um máximo de 500 de transações por segundo por aplicativo de proxy de aplicativo</li><li>Um máximo de 750 transações por segundo para o locatário</li></ul><br/>Uma transação é definida como uma única solicitação HTTP e uma resposta para um recurso exclusivo. Quando limitado, os clientes receberão uma resposta de 429 (muitas solicitações). |
| Painel de Acesso |<ul><li>Não existe nenhum limite para o número de aplicações apresentado no Painel de Acesso por utilizador. Isto aplica-se aos utilizadores aos quais foram atribuídas licenças para o Azure AD Premium ou o Enterprise Mobility Suite.</li><li>Um máximo de dez mosaicos de aplicação é apresentado no Painel de Acesso para cada utilizador. Esse limite se aplica a usuários que recebem licenças para Azure AD Gratuito plano de licença. Exemplos de mosaicos de aplicação incluem o Box, Salesforce ou Dropbox. Este limite não se aplica às contas de administrador.</li></ul> |
| Relatórios | Pode ser visto um máximo de 1000 linhas ou transferido em qualquer relatório. Quaisquer dados adicionais são truncados. |
| Unidades administrativas | Um recurso do Azure AD pode ser um membro de no máximo 30 unidades administrativas. |
| Funções e permissões de administrador | <ul><li>Um grupo não pode ser adicionado como [proprietário](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Um grupo não pode ser atribuído a uma [função](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>A capacidade dos usuários de ler informações de diretório de outros usuários não pode ser restrita fora do comutador de todo o locatário para desabilitar o acesso de todos os usuários não administradores a todas as informações de diretório (não recomendado). Mais informações sobre as permissões padrão [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Pode levar até 15 minutos ou sair/entrar antes que as adições e as revogações de associação de função de administrador entrem em vigor.</li></ul> |
