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
ms.openlocfilehash: e6e1014ac20ef70f21344ec6ece5627eccb7ba66
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67148452"
---
Aqui estão as restrições de utilização e outros limites de serviço do serviço do Azure Active Directory (Azure AD).

| Category | Limites |
| --- | --- |
| Diretórios | Um único utilizador pode pertencer a um máximo de 500 diretórios do Azure AD como membro ou convidado.<br/>Um único utilizador pode criar um máximo de 20 diretórios. |
| Domínios | Pode adicionar um máximo de 900 nomes de domínio gerido. Se configurar todos os seus domínios para federação com o Active Directory no local, pode adicionar um máximo de 450 nomes de domínio em cada diretório. |
| Objetos |<ul><li>Os utilizadores da edição Gratuita do Azure Active Directory podem criar um máximo de 50 000 objetos num único diretório por predefinição. Se tiver pelo menos um domínio verificado, a quota de serviço de diretório predefinida no Azure AD é aumentada para 300 000 objetos. </li><li>Um utilizador não-administrador não pode criar mais do que 250 objetos. Tanto os objetos ativos como os objetos eliminados que estão disponíveis para restaurar contam para esta quota. Apenas os objetos eliminados que tenham sido eliminados há menos de 30 dias estão disponíveis para restaurar. Os objetos eliminados que já não estejam disponíveis para restaurar contam para esta quota com um valor de um quarto durante 30 dias. Talvez deva [atribuir uma função de administrador](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) aos utilizadores não administradores que, no exercício das suas funções, provavelmente excedem esta quota repetidamente.</li></ul> |
| Extensões de esquema |<ul><li>As extensões de tipo de cadeia podem ter até 256 carateres. </li><li>As extensões de tipo binário estão limitadas a 256 bytes.</li><li>Apenas cem valores de extensão (em *todos* os tipos e *todas* as aplicações) podem ser escritos para qualquer objeto único.</li><li>Apenas as entidades User, Group, TenantDetail, Device, Application e ServicePrincipal podem ser expandidas com os atributos de valor único do tipo de cadeia ou de tipo de binário.</li><li>As extensões de esquema estão disponíveis apenas na pré-visualização da versão 1.21 da Graph API. A aplicação tem de ter acesso de escrita para registar uma extensão.</li></ul> |
| Aplicações |No máximo, cem utilizadores podem ser proprietários de uma única aplicação. |
| Grupos |<ul><li>No máximo, cem utilizadores podem ser proprietários de um único grupo.</li><li>Não existe um limite no número de objetos que podem ser membros de um único grupo.</li><li>Um utilizador pode ser membro de qualquer número de grupos.</li><li>O número de membros de um grupo que pode sincronizar a partir do Active Directory no local para o Azure Active Directory com o Azure AD Connect está limitado a 50 000 membros.</li></ul> |
| Proxy da aplicação | <ul><li>Um máximo de 500 de transações por segundo por aplicativo de proxy de aplicativo</li><li>Um máximo de 750 transações por segundo para o locatário</li></ul><br/>Uma transação é definida como uma única solicitação HTTP e uma resposta para um recurso exclusivo. Quando limitado, os clientes receberão uma resposta de 429 (muitas solicitações). |
| Painel de Acesso |<ul><li>Não existe nenhum limite para o número de aplicações apresentado no Painel de Acesso por utilizador. Isto aplica-se aos utilizadores aos quais foram atribuídas licenças para o Azure AD Premium ou o Enterprise Mobility Suite.</li><li>Um máximo de dez mosaicos de aplicação é apresentado no Painel de Acesso para cada utilizador. Este limite aplica-se aos utilizadores aos quais foram atribuídas licenças para as edições Azure AD Gratuito ou Azure AD Básico do Azure Active Directory. Exemplos de mosaicos de aplicação incluem o Box, Salesforce ou Dropbox. Este limite não se aplica às contas de administrador.</li></ul> |
| Relatórios | Pode ser visto um máximo de 1000 linhas ou transferido em qualquer relatório. Quaisquer dados adicionais são truncados. |
| Unidades administrativas | Um objeto não pode ser um membro de mais de 30 unidades administrativas. |
| Funções e permissões de administrador | <ul><li>Um grupo não pode ser adicionado como [proprietário](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Um grupo não pode ser atribuído a uma [função](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>A capacidade dos usuários de ler informações de diretório de outros usuários não pode ser restrita fora do comutador de todo o locatário para desabilitar o acesso de todos os usuários não administradores a todas as informações de diretório (não recomendado). Mais informações sobre as permissões padrão [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Pode levar até 15 minutos ou sair/entrar antes que as adições e as revogações de associação de função de administrador entrem em vigor.</li></ul> |
