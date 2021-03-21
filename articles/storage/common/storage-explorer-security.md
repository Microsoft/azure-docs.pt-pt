---
title: Guia de segurança do Azure Storage Explorer | Microsoft Docs
description: Orientação de segurança para o Azure Storage Explorer
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783764"
---
# <a name="azure-storage-explorer-security-guide"></a>Guia de segurança do Azure Storage Explorer

O Microsoft Azure Storage Explorer permite-lhe trabalhar facilmente com os dados do Azure Storage de forma segura e segura no Windows, macOS e Linux. Seguindo estas orientações, pode garantir que os seus dados permanecem protegidos.

## <a name="general"></a>Geral

- **Utilize sempre a versão mais recente do Storage Explorer.** As versões do Storage Explorer podem conter atualizações de segurança. Manter-se atualizado ajuda a garantir a segurança geral.
- **Só se ligue aos recursos em que confia.** Os dados que descarrega de fontes não fidedídas podem ser maliciosos, e o envio de dados para uma fonte não fidedquirte pode resultar em dados perdidos ou roubados.
- **Utilize HTTPS sempre que possível.** O Explorador de Armazenamento utiliza HTTPS por predefinição. Alguns cenários permitem-lhe utilizar HTTP, mas HTTP deve ser usado apenas como último recurso.
- **Certifique-se de que apenas as permissões necessárias são dadas às pessoas que precisam delas.** Evite ser excessivamente permissivo ao conceder a qualquer pessoa acesso aos seus recursos.
- **Tenha cuidado ao executar operações críticas.** Certas operações, tais como a eliminação e substituição, são irreversíveis e podem causar perda de dados. Certifique-se de que está a trabalhar com os recursos corretos antes de executar estas operações.

## <a name="choosing-the-right-authentication-method"></a>Escolher o método de autenticação certo

O Storage Explorer fornece várias formas de aceder aos seus recursos de Armazenamento Azure. Seja qual for o método que escolher, aqui estão as nossas recomendações.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

A forma mais fácil e segura de aceder aos seus recursos de Armazenamento Azure é iniciar sômss com a sua conta Azure. Iniciar sessão utiliza a autenticação Azure AD, que lhe permite:

- Dar acesso a utilizadores e grupos específicos.
- Revogar o acesso a utilizadores e grupos específicos a qualquer momento.
- Impor condições de acesso, tais como exigir a autenticação de vários fatores.

Recomendamos a utilização da autenticação Azure AD sempre que possível.

Esta secção descreve as duas tecnologias baseadas em AD Azure que podem ser usadas para garantir os seus recursos de armazenamento.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Controlo de acesso baseado em funções do Azure (RBAC do Azure)

[O controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md) dá-lhe um controlo de acesso fino sobre os seus recursos Azure. As funções e permissões azure podem ser geridas a partir do portal Azure.

O Storage Explorer suporta o acesso do Azure RBAC a Contas de Armazenamento, Blobs e Filas. Se precisar de acesso a Ações ou Tabelas de Ficheiros, terá de atribuir funções Azure que concedam permissão para listar chaves de conta de armazenamento.

#### <a name="access-control-lists-acls"></a>Listas de controlo de acesso (ACL)

[As listas de controlo de acesso (ACLs)](../blobs/data-lake-storage-access-control.md) permitem controlar o acesso ao ficheiro e ao nível das pastas em recipientes blob ADLS Gen2. Pode gerir os seus ACLs utilizando o Storage Explorer.

### <a name="shared-access-signatures-sas"></a>Assinaturas de acesso partilhado (SAS)

Se não puder utilizar a autenticação Azure AD, recomendamos a utilização de assinaturas de acesso partilhado. Com assinaturas de acesso partilhado, pode:

- Fornecer acesso anónimo limitado a recursos seguros.
- Revogar imediatamente um SAS se for gerado a partir de uma política de acesso partilhado (SAP).

No entanto, com assinaturas de acesso partilhado, não pode:

- Restringir quem pode usar um SAS. Um SAS válido pode ser usado por qualquer pessoa que o tenha.
- Revogar uma SAS se não for gerada a partir de uma política de acesso partilhado (SAP).

Ao utilizar o SAS no Storage Explorer, recomendamos as seguintes diretrizes:

- **Limitar a distribuição de fichas SAS e URIs.** Apenas distribua fichas SAS e URIs a indivíduos de confiança. Limitar a distribuição sas reduz a possibilidade de um SAS poder ser mal utilizado.
- **Utilize apenas fichas SAS e URIs de entidades em que confie.**
- **Utilize políticas de acesso partilhado (SAP) ao gerar fichas SAS e URIs, se possível.** Um SAS baseado numa política de acesso partilhado é mais seguro do que um SAS nu, porque o SAS pode ser revogado eliminando o SAP.
- **Gerar fichas com acesso mínimo de recursos e permissões.** As permissões mínimas limitam os danos potenciais que podem ser causados se um SAS for utilizado indevidamente.
- **Gere fichas que só são válidas durante o tempo necessário.** Uma vida útil curta é especialmente importante para o SAS nu, porque não há como revogá-los uma vez gerados.

> [!IMPORTANT]
> Ao partilhar tokens SAS e URIs para efeitos de resolução de problemas, como em pedidos de serviço ou relatórios de bugs, sempre redirecione pelo menos a parte de assinatura do SAS.

### <a name="storage-account-keys"></a>Chaves de contas de armazenamento

As chaves da conta de armazenamento concedem acesso ilimitado aos serviços e recursos dentro de uma conta de armazenamento. Por esta razão, recomendamos limitar a utilização de chaves para aceder a recursos no Storage Explorer. Utilize funcionalidades Azure RBAC ou SAS para fornecer acesso.

Algumas funções da Azure concedem permissão para recuperar chaves de conta de armazenamento. Os indivíduos com estas funções podem efetivamente contornar permissões concedidas ou negadas pela Azure RBAC. Recomendamos não conceder esta permissão a menos que seja necessário.

O Storage Explorer tentará utilizar as chaves da conta de armazenamento, se disponível, para autenticar pedidos. Pode desativar esta função em Definições **(Contas de armazenamento > serviços > Desativar o Uso das Chaves**). Algumas funcionalidades não suportam o Azure RBAC, como trabalhar com contas de armazenamento clássicos. Tais funcionalidades ainda requerem teclas e não são afetadas por esta definição.

Se tiver de utilizar as chaves para aceder aos seus recursos de armazenamento, recomendamos as seguintes orientações:

- **Não partilhe as chaves da sua conta com ninguém.**
- **Trate as chaves da sua conta de armazenamento como palavras-passe.** Se tiver de tornar as suas chaves acessíveis, utilize soluções de armazenamento seguras, como [o Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Se acredita que uma chave de conta de armazenamento foi partilhada ou distribuída por engano, pode gerar novas chaves para a sua conta de armazenamento a partir do portal Azure.

### <a name="public-access-to-blob-containers"></a>Acesso público a contentores de bolhas

O Storage Explorer permite modificar o nível de acesso dos seus recipientes de armazenamento Azure Blob. Os recipientes de bolhas não privados permitem a qualquer pessoa anónima ler o acesso aos dados nesses contentores.

Ao permitir o acesso público a um recipiente de bolhas, recomendamos as seguintes orientações:

- **Não permita o acesso do público a um recipiente de bolhas que possa conter quaisquer dados potencialmente sensíveis.** Certifique-se de que o seu recipiente blob está livre de todos os dados privados.
- **Não carrede dados potencialmente sensíveis para um recipiente de bolhas com acesso blob ou contentor.** 

## <a name="next-steps"></a>Passos seguintes

- [Recomendações de segurança](../blobs/security-recommendations.md)