---
title: Delegado por tarefa de administração - Azure Ative Directory / Microsoft Docs
description: Funções para delegar para tarefas de identidade no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e83ff37140369cb073f4bdc2bd24ac08ee9b1ab
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379099"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Funções de administrador por tarefa de administração no Azure Ative Directory

Neste artigo, pode encontrar as informações necessárias para restringir as permissões de administrador de um utilizador, atribuindo funções menos privilegiadas no Azure Ative Directory (Azure AD). Encontrará tarefas de administrador organizadas pela área de recurso e a função menos privilegiada necessária para executar cada tarefa, juntamente com funções adicionais de Administrador Não Global que possam desempenhar a tarefa.

## <a name="application-proxy"></a>Proxy da aplicação

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configure aplicação proxy app | Administrador de aplicação | 
Configure as propriedades do grupo de conector | Administrador de aplicação | 
Crie o registo de aplicações quando a capacidade é desativada para todos os utilizadores | Desenvolvedor de aplicações | Administrador de aplicação na nuvem, administrador de aplicações
Criar grupo de conector | Administrador de aplicação | 
Eliminar grupo de conector | Administrador de aplicação | 
Desativar proxy de aplicações | Administrador de aplicação | 
Serviço de conector de descarregamento | Administrador de aplicação | 
Leia todas as configurações | Administrador de aplicação | 

## <a name="external-identitiesb2c"></a>Identidades Externas/B2C

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Criar diretórios Azure AD B2C | Todos os utilizadores não convidados[(ver documentação)](../fundamentals/users-default-permissions.md) | 
Criar aplicações B2C | Administrador Global | 
Criar aplicações empresariais | Administrador de Aplicações na Cloud | Administrador da Aplicação
Criar, ler, atualizar e eliminar políticas B2C | Administrador de Política B2C IEF | 
Criar, ler, atualizar e eliminar fornecedores de identidade | Administrador de fornecedor de identidade externa | 
Criar, ler, atualizar e eliminar fluxos de utilizador de redefinição de palavra-passe | Administrador de fluxo de id externo | 
Criar, ler, atualizar e eliminar fluxos de utilizadores de edição de perfis | Administrador de fluxo de id externo | 
Criar, ler, atualizar e eliminar fluxos de utilizador de entrada | Administrador de fluxo de id externo | 
Criar, ler, atualizar e eliminar o fluxo de utilizador de inscrição |Administrador de fluxo de id externo | 
Criar, ler, atualizar e eliminar atributos do utilizador | Administrador de atributo de fluxo de id do utilizador externo | 
Criar, ler, atualizar e eliminar utilizadores | Administrador de Utilizadores
Leia todas as configurações | Leitor global | 
Leia os registos de auditoria b2C | Leitor global[(ver documentação)](../../active-directory-b2c/faq.md) | 

> [!NOTE]
> Os leitores Azure AD B2C Global não têm as mesmas permissões que os administradores globais da Azure AD. Se tiver privilégios globais de administrador Azure AD B2C, certifique-se de que está num diretório AZure AD B2C e não num diretório AD AZure.

## <a name="company-branding"></a>Imagem corporativa

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configurar o branding da empresa | Administrador Global | 
Leia todas as configurações | Leitores de diretório | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md)

## <a name="company-properties"></a>Propriedades da empresa

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configure propriedades da empresa | Administrador Global | 

## <a name="connect"></a>Ligar-se

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Autenticação passthrough | Administrador Global  | 
Leia todas as configurações | Leitor global | Administrador Global  |
Único sinal sem emenda | Administrador Global  | 

## <a name="cloud-provisioning"></a>Provisionamento de Nuvens

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Autenticação passthrough | Administrador de Identidade Híbrida  | 
Leia todas as configurações | Leitor global | Administrador de Identidade Híbrida  |
Único sinal sem emenda | Administrador de Identidade Híbrida  | 

## <a name="connect-health"></a>Connect Health

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Adicionar ou apagar serviços | Proprietário[(ver documentação)](../hybrid/how-to-connect-health-operations.md) | 
Aplicar correções para sincronizar erro | Contribuinte[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Proprietário
Configurar notificações | Contribuinte[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Proprietário
Configurar definições | Proprietário[(ver documentação)](../hybrid/how-to-connect-health-operations.md) | 
Configure notificações de sincronização | Contribuinte[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Proprietário
Leia os relatórios de segurança da ADFS | Leitor de Segurança | Colaborador, Proprietário
Leia todas as configurações | Leitor[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Colaborador, Proprietário
Leia erros de sincronização | Leitor[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Colaborador, Proprietário
Ler serviços de sincronização | Leitor[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Colaborador, Proprietário
Ver métricas e alertas | Leitor[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Colaborador, Proprietário
Ver métricas e alertas | Leitor[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Colaborador, Proprietário
Ver métricas e alertas de serviço sincronizado | Leitor[(ver documentação)](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) | Colaborador, Proprietário

## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Gerir domínios | Administrador Global | 
Leia todas as configurações | Leitores de diretório | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md)

## <a name="domain-services"></a>Serviços de Domínio

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Criar exemplo de serviços de domínio Azure AD | Administrador Global | 
Executar todas as tarefas dos Serviços de Domínio AZure AD | Grupo de Administradores Azure AD DC[(ver documentação)](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain) | 
Leia todas as configurações | Leitor na subscrição Azure contendo serviço AD DS | 

## <a name="devices"></a>Dispositivos

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Dispositivo de desativação | Administrador de dispositivos em nuvem | 
Ativar dispositivo | Administrador de dispositivos em nuvem | 
Ler configuração básica | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md) | 
Leia as teclas BitLocker | Leitor de Segurança | Administrador de password, administrador de segurança

## <a name="enterprise-applications"></a>Aplicações Empresariais

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Consentimento para quaisquer permissões delegadas | Administrador de aplicação em nuvem | Administrador de aplicação
Consentimento para permissões de aplicação que não incluam o Microsoft Graph | Administrador de aplicação em nuvem | Administrador de aplicação
Consentimento para permissões de aplicação no Microsoft Graph | Administrador privilegiado | 
Consentimento para aplicações que acedam a dados próprios | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md) | 
Criar aplicação de empresa | Administrador de aplicação em nuvem | Administrador de aplicação
Gerir o Proxy de Aplicações | Administrador de aplicação | 
Gerir as definições do utilizador | Administrador Global | 
Leia a revisão de acesso de um grupo ou de uma app | Leitor de Segurança | Administrador de Segurança, Administrador de Utilizador
Leia todas as configurações | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md) | 
Atualizar as atribuições de aplicações da empresa | Proprietário de aplicações empresariais[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar os proprietários de aplicações empresariais | Proprietário de aplicações empresariais[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar propriedades de aplicações empresariais | Proprietário de aplicações empresariais[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar o fornecimento de aplicações da empresa | Proprietário de aplicações empresariais[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar o self-service da aplicação da empresa | Proprietário de aplicações empresariais[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar propriedades de s-on única | Proprietário de aplicações empresariais[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de aplicação em nuvem, administrador de aplicações

## <a name="entitlement-management"></a>Gestão de direitos
Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Adicionar recursos a um catálogo | Administrador de utilizadores | Com a gestão de direitos, pode delegar esta tarefa ao proprietário do catálogo[(ver documentação)](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)
Adicione sites SharePoint Online para catalogar | Administrador global


## <a name="groups"></a>Grupos

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Atribuir licença | Administrador de utilizadores | 
Criar grupos | Administrador de grupos | Administrador de utilizadores
Criar, atualizar ou eliminar a revisão de acesso de um grupo ou de uma aplicação | Administrador de utilizadores | 
Gerir a expiração do grupo | Administrador de utilizadores | 
Gerir definições do grupo | Administrador de Grupos | Administrador de Utilizadores | 
Leia todas as configurações (exceto a adesão escondida) | Leitores de diretório | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md)
Ler membros escondidos | Membro do grupo | Proprietário do grupo, administrador de password, administrador de troca, administrador sharePoint, administrador de equipas, administrador do utilizador
Ler membros de grupos com adesão oculta | Administrador helpdesk | Administrador de utilizadores, administrador de equipas
Revogar licença | Administrador de licenças | Administrador de utilizadores
Atualizar a adesão ao grupo | Dono do grupo[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de utilizadores
Atualizar os proprietários do grupo | Dono do grupo[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de utilizadores
Atualizar propriedades de grupo | Dono do grupo[(ver documentação)](../fundamentals/users-default-permissions.md) | Administrador de utilizadores
Eliminar grupo | Administrador de grupos | Administrador de utilizadores

## <a name="identity-protection"></a>Identity Protection

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configure notificações de alerta| Administrador de Segurança | 
Configure e permita ou desative a política de MFA| Administrador de Segurança | 
Configurar e permitir ou desativar a política de risco de inscrição| Administrador de Segurança | 
Configurar e ativar ou desativar a política de risco do utilizador | Administrador de Segurança | 
Configurar digestão semanal | Administrador de Segurança| 
Dispensar todas as deteções de risco | Administrador de Segurança | 
Corrigir ou descartar vulnerabilidade | Administrador de Segurança | 
Leia todas as configurações | Leitor de Segurança | 
Leia todas as deteções de risco | Leitor de Segurança | 
Ler vulnerabilidades | Leitor de Segurança | 

## <a name="licenses"></a>Licenças

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Atribuir licença | Administrador de licenças | Administrador de utilizadores
Leia todas as configurações | Leitores de diretório | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md)
Revogar licença | Administrador de licenças | Administrador de utilizadores
Tente ou compre subscrição | Administrador de faturação | 


## <a name="monitoring---audit-logs"></a>Monitorização - Registos de auditoria

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Ler registos de auditoria | Leitor de relatórios | Leitor de Segurança, Administrador de Segurança

## <a name="monitoring---sign-ins"></a>Monitorização - Inscrições

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Ler registos de inscrição | Leitor de relatórios | Leitor de Segurança, Administrador de Segurança

## <a name="multi-factor-authentication"></a>Autenticação multifator

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Eliminar todas as palavras-passe de aplicações existentes geradas pelos utilizadores selecionados | Administrador Global | 
Desativar o MFA | Administrador Global | 
Ativar MFA | Administrador Global | 
Gerir as definições de serviço MFA | Administrador Global | 
Exigir que os utilizadores selecionados forneçam novamente métodos de contacto | Administrador de Autenticação | 
Restaurar a autenticação de vários fatores em todos os dispositivos lembrados  | Administrador de Autenticação | 

## <a name="mfa-server"></a>Servidor MFA

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Utilizadores de bloco/desbloqueio | Administrador Global | 
Configurar bloqueio de conta | Administrador Global | 
Configurar regras de caching | Administrador Global | 
Alerta de fraude de configuração | Administrador Global
Configurar notificações | Administrador Global | 
Configure um bypass único | Administrador Global | 
Configurar as definições de chamadas telefónicas | Administrador Global | 
Configure fornecedores | Administrador Global | 
Configurar as definições do servidor | Administrador Global | 
Ler relatório de atividades | Leitor global | 
Leia todas as configurações | Leitor global | 
Ler estado do servidor | Leitor global |  

## <a name="organizational-relationships"></a>Relações organizacionais

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Gerir fornecedores de identidade | Administrador de fornecedor de identidade externa | 
Gerir definições | Administrador Global | 
Gerir termos de utilização | Administrador Global | 
Leia todas as configurações | Leitor global | 

## <a name="password-reset"></a>Reposição de palavras-passe

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configurar métodos de autenticação | Administrador Global |
Configurar a personalização | Administrador Global |
Notificação de configure | Administrador Global |
Configurar a integração no local | Administrador Global |
Configure propriedades de reset de palavra-passe | Administrador de Utilizadores | Administrador Global
Configure registo | Administrador Global |
Leia todas as configurações | Administrador de Segurança | Administrador de Utilizadores |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Atribuir utilizadores a funções | Administrador privilegiado | 
Configurar as definições de função | Administrador privilegiado | 
Ver atividade de auditoria | Leitor de segurança | 
Ver membros de funções | Leitor de segurança | 

## <a name="roles-and-administrators"></a>Funções e administradores

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Gerir atribuições de funções | Administrador privilegiado | 
Leia a revisão de acesso de um papel de AD Azure  | Leitor de Segurança | Administrador de segurança, administrador privilegiado
Leia todas as configurações | Papel de utilizador predefinido[(ver documentação)](../fundamentals/users-default-permissions.md) | 

## <a name="security---authentication-methods"></a>Segurança - Métodos de autenticação

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configurar métodos de autenticação | Administrador Global | 
Proteção de senhas de configuração | Administrador de segurança
Configurar o bloqueio inteligente | Administrador de segurança
Leia todas as configurações | Leitor global | 

## <a name="security---conditional-access"></a>Segurança - Acesso Condicional

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configure endereços IP fidedignos da MFA | Administrador de acesso condicional | 
Criar controlos personalizados | Administrador de acesso condicional | Administrador de segurança
Criar localizações nomeadas | Administrador de acesso condicional | Administrador de segurança
Criar políticas | Administrador de acesso condicional | Administrador de segurança
Criar termos de utilização | Administrador de acesso condicional | Administrador de segurança
Criar certificado de conectividade VPN | Administrador de acesso condicional | Administrador de segurança
Excluir a política clássica | Administrador de acesso condicional | Administrador de segurança
Eliminar termos de utilização | Administrador de acesso condicional | Administrador de segurança
Eliminar certificado de conectividade VPN | Administrador de acesso condicional | Administrador de segurança
Desativar a política clássica | Administrador de acesso condicional | Administrador de segurança
Gerir controlos personalizados | Administrador de acesso condicional | Administrador de segurança
Gerir localizações nomeadas | Administrador de acesso condicional | Administrador de segurança
Gerir termos de utilização | Administrador de acesso condicional | Administrador de segurança
Leia todas as configurações | Leitor de segurança | Administrador de segurança
Ler locais nomeados | Leitor de segurança | Administrador de acesso condicional, administrador de segurança

## <a name="security---identity-security-score"></a>Segurança - Pontuação de segurança de identidade

Tarefa | Papel menos privilegiado | Funções adicionais | 
---- | --------------------- | ----------------
Leia todas as configurações | Leitor de segurança | Administrador de segurança
Leia a pontuação de segurança | Leitor de segurança | Administrador de segurança
Atualizar o estado do evento | Administrador de segurança | 

## <a name="security---risky-sign-ins"></a>Segurança - Entradas arriscadas

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Leia todas as configurações | Leitor de Segurança | 
Leia insusição arriscada | Leitor de Segurança | 

## <a name="security---users-flagged-for-risk"></a>Segurança - Utilizadores sinalizados para risco

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Dispensar todos os eventos | Administrador de Segurança | 
Leia todas as configurações | Leitor de Segurança | 
Leia os utilizadores sinalizados para o risco | Leitor de Segurança | 

## <a name="users"></a>Utilizadores

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Adicione o utilizador ao papel de diretório | Administrador privilegiado | 
Adicionar utilizador ao grupo | Administrador de utilizadores | 
Atribuir licença | Administrador de licenças | Administrador de utilizadores
Criar utilizador convidado | Convidado convidado | Administrador de utilizadores
Criar utilizador | Administrador de utilizadores | 
Eliminar utilizadores | Administrador de utilizadores | 
Tokens de atualização invalidado de administradores limitados (ver documentação) | Administrador de utilizadores | 
Tokens de atualização invalidado de administradores não administrativos (ver documentação) | Administrador de palavras-passe | Administrador de utilizadores
Tokens de atualização invalidado de administradores privilegiados (ver documentação) | Administrador de Autenticação Privilegiada | 
Ler configuração básica | Papel de utilizador predefinido[(ver documentação](../fundamentals/users-default-permissions.md) | 
Redefinir a palavra-passe para administradores limitados (ver documentação) | Administrador de utilizadores | 
Redefinir a palavra-passe dos administradores não administrativos (ver documentação) | Administrador de palavras-passe | Administrador de utilizadores
Redefinir a palavra-passe dos administradores privilegiados | Administrador de Autenticação Privilegiada | 
Revogar licença | Administrador de licenças | Administrador de utilizadores
Atualizar todas as propriedades, exceto o nome principal do utilizador | Administrador de utilizadores | 
Atualizar Nome Principal do Utilizador para administradores limitados (ver documentação) | Administrador de utilizadores | 
Atualizar propriedade do nome principal do utilizador em administradores privilegiados (ver documentação) | Administrador Global | 
Atualizar as definições do utilizador | Administrador Global | 
Atualizar métodos de autenticação | Administrador de Autenticação | Administrador de Autenticação Privilegiada, Administrador Global


## <a name="support"></a>Suporte

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Envie bilhete de apoio | Administrador de Serviços | Administrador de Aplicação, Administrador de Proteção de Informação Azure, Administrador de Faturação, Administrador de Aplicação na Nuvem, Administrador de Conformidade, Administrador dinâmico 365, Administrador de Análise de Ambiente, Administrador de Intercâmbio, Administrador de Password, Administrador Intune, Administrador de Negócios, Administrador de Power BI, Administrador de Autenticação Privilegiada, Administrador de Partilha de Comunicações, Administrador de Equipas, Administrador de Equipas, Administrador de Análise de Utilizador, Administrador de Análise de Trabalho

## <a name="next-steps"></a>Passos seguintes

* [Como atribuir ou remover funções de administrador ad azul](manage-roles-portal.md)
* [Referência de funções de administrador da AD Azure](permissions-reference.md)