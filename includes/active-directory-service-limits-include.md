---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554764"
---
Aqui estão as restrições de utilização e outros limites de serviço para o serviço do Azure Active Directory (Azure AD).

| Categoria | Limites |
| --- | --- |
| Diretórios | Um único utilizador pode pertencer a um máximo de 500 diretórios do Azure AD como membro ou convidado.<br/>Um único utilizador pode criar um máximo de 20 diretórios. |
| Domínios | Pode adicionar não mais de 900 nomes de domínio gerido. Se configurar todos os seus domínios para federação com o Active Directory no local, pode adicionar nomes de domínio não mais de 450 em cada diretório. |
| Objetos |<ul><li>Um máximo de 500.000 objetos pode ser criado num único diretório por utilizadores da edição gratuita do Azure Active Directory.</li><li>Um utilizador não-administrador não pode criar mais do que 250 objetos. Objetos do Active Directory e objetos eliminados que estão disponíveis para restaurar contam para esta quota. Apenas objetos que foram eliminados eliminados há menos de 30 dias estão disponíveis para restaurar. Eliminar objetos que já não estão disponíveis para restaurar contam para esta quota num valor de um quarto durante 30 dias. Talvez [atribuir uma função de administrador](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) para usuários não-administradores, que são provável que repetidamente exceder esta quota no decorrer de suas tarefas regulares.</li></ul> |
| Extensões de esquema |<ul><li>Extensões de tipo de cadeia de caracteres podem ter um máximo de 256 carateres. </li><li>Extensões de tipo binário estão limitadas a 256 bytes.</li><li>Apenas 100 valores de extensão, na *todos os* tipos e *todos os* aplicações, pode ser escrito para qualquer objeto único.</li><li>Entidades de utilizador, grupo, TenantDetail, dispositivo, aplicação e ServicePrincipal só podem ser estendidas com o tipo de cadeia de caracteres ou atributos de valor único de tipo binário.</li><li>Extensões de esquema estão disponíveis apenas na pré-visualização de versão 1.21 da Graph API. A aplicação tem de ter acesso de escrita para registar uma extensão.</li></ul> |
| Aplicações |Um máximo de 100 utilizadores pode ser proprietários de uma única aplicação. |
| Grupos |<ul><li>Um máximo de 100 utilizadores pode ser proprietários de um único grupo.</li><li>Qualquer número de objetos pode ser membros de um único grupo.</li><li>Um utilizador pode ser um membro de qualquer número de grupos.</li><li>O número de membros de um grupo que pode sincronizar do Active Directory no local ao Azure Active Directory com o Azure AD Connect é limitado a 50.000 membros.</li></ul> |
| Painel de Acesso |<ul><li>Não existe nenhum limite para o número de aplicativos que podem ser vistos no painel de acesso por utilizador. Isto aplica-se aos utilizadores licenças atribuídas para o Azure AD Premium ou Enterprise Mobility Suite.</li><li>Pode ser visto um máximo de 10 mosaicos de aplicação no painel de acesso para cada utilizador. Este limite aplica-se para os utilizadores que são atribuídos licenças gratuitamente ou as edições do Azure AD básico do Azure Active Directory. Exemplos de mosaicos de aplicação incluem o Box, Salesforce ou Dropbox. Este limite não se aplica a contas de administrador.</li></ul> |
| Relatórios | Pode ser visto um máximo de 1000 linhas ou transferido em qualquer relatório. Quaisquer dados adicionais são truncados. |
| Unidades administrativas | Um objeto não pode ser um membro de mais de 30 unidades administrativas. |
| Permissões e funções de administrador | <li>Não é possível adicionar um grupo como proprietário.<li>Não é possível atribuir um grupo a uma função.<li>Utilizador as permissões não podem ser alteradas, exceto para comutadores de inquilino, que são as definições de utilizador no Azure AD por predefinição. |
