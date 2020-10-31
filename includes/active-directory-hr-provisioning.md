---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135398"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Aplicação cloud HR para o provisionamento do utilizador do Azure Ative Directory

Historicamente, o pessoal de TI tem confiado em métodos manuais para criar, atualizar e apagar funcionários. Usaram métodos como o upload de ficheiros CSV ou scripts personalizados para sincronizar dados dos funcionários. Estes processos de provisionamento são propensos a erros, inseguros e difíceis de gerir.

Para gerir os ciclos de vida identitários dos colaboradores, fornecedores ou trabalhadores contingentes, o serviço de prestação de serviços de fornecimento de [utilizadores Azure Ative (Azure AD)](../articles/active-directory/app-provisioning/user-provisioning.md) oferece integração com aplicações de recursos humanos (RH) baseadas na nuvem. Exemplos de aplicações incluem Workday ou SuccessFactors.

A Azure AD utiliza esta integração para permitir os seguintes fluxos de trabalho de aplicação hr em nuvem (app):

- **Fornecimento de utilizadores ao Ative Directory:** Fornecendo conjuntos selecionados de utilizadores de uma aplicação de HR em nuvem em um ou mais domínios do Ative Directory.
- **Fornecimento de utilizadores apenas em nuvem para Azure AD:** Em cenários onde o Ative Directory não é utilizado, fornecendo utilizadores diretamente da aplicação cloud HR para Azure AD.
- **Escreva de volta para a aplicação hr em nuvem:** Escreva os endereços de e-mail e os atributos de nome de utilizador do Azure AD de volta para a aplicação hr na nuvem.


## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de fornecimento de utilizadores Azure AD permite automatizar os seguintes cenários de gestão do ciclo de vida de identidade baseados em RH:

- **Contratação de novos funcionários:** Quando um novo funcionário é adicionado à aplicação cloud HR, uma conta de utilizador é criada automaticamente no Ative Directory e Azure AD com a opção de escrever de volta o endereço de e-mail e o nome de utilizador atribui à aplicação hr na nuvem.
- **Atributo do empregado e atualizações de perfil:** Quando um registo de empregados como nome, título ou gestor é atualizado na aplicação cloud HR, a sua conta de utilizador é automaticamente atualizada no Ative Directory e no Azure AD.
- **Rescisões dos empregados:** Quando um empregado é encerrado na aplicação cloud HR, a sua conta de utilizador é automaticamente desativada em Ative Directory e Azure AD.
- **Recontrações de empregados:** Quando um empregado é recontratado na aplicação cloud HR, a sua conta antiga pode ser automaticamente reativada ou reprovisionada para Ative Directory e Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Para quem é que esta integração é mais adequada?

A integração de aplicativos hr em nuvem com o fornecimento de utilizadores AZure AD é ideal para organizações que:

- Pretenda uma solução pré-construída baseada na nuvem para o fornecimento de rh em nuvem.
- Requerer o fornecimento direto do utilizador da aplicação hr em nuvem para Ative Directory ou Azure AD.
- Exigir que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da aplicação cloud HR.
- Exija que os utilizadores se juntem, se movam e que os utilizadores sejam sincronizados com uma ou mais florestas, domínios e OUs do Ative Directory, com base apenas em informações de alteração detetadas na aplicação cloud HR.
- Use o Office 365 para e-mail.


### <a name="key-benefits"></a>Principais vantagens

Esta capacidade de provisionamento de TI orientado por RH oferece os seguintes benefícios significativos para o negócio:

- **Aumentar a produtividade:** Pode agora automatizar a atribuição de contas de utilizador e licenças do Office 365 e fornecer acesso a grupos-chave. Automatizar tarefas dá às novas contratações acesso imediato às suas ferramentas de trabalho e aumenta a produtividade.
- **Gerir o risco:** Pode aumentar a segurança automatizando alterações com base no estado dos funcionários ou membros do grupo com dados que fluem a partir da aplicação cloud HR. Automatizar alterações garante que as identidades dos utilizadores e o acesso às principais aplicações atualizam-se automaticamente quando os utilizadores fazem a transição ou saem da organização.
- **Abordar a conformidade e a governação:** O Azure AD suporta registos de auditoria nativos para pedidos de provisionamento de utilizadores realizados por aplicações de sistemas de origem e alvo. Com a auditoria, é possível rastrear quem tem acesso às aplicações a partir de um único ecrã.
- **Gerir o custo:** O provisionamento automático reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Reduz a necessidade de soluções de fornecimento de utilizadores desenvolvidas por medida construídas ao longo do tempo utilizando plataformas antigas e desatualizadas.
