---
title: Delegar funções por tarefa de administração - Diretório Ativo Azure / Microsoft Docs
description: Funções para delegar em tarefas de identidade no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/03/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd24650c9bf0c4de155b5bfc8723cfa1fef01548
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755406"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Funções de administrador por tarefa de administração no Diretório Ativo azure

Neste artigo, pode encontrar as informações necessárias para restringir as permissões de administrador de um utilizador, atribuindo papéis menos privilegiados no Azure Ative Directory (Azure AD). Encontrará tarefas de administrador organizadas por área de recurso e a função menos privilegiada necessária para executar cada tarefa, juntamente com funções adicionais de Administrador Não Global que possam executar a tarefa.

## <a name="application-proxy"></a>Proxy da aplicação

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configure app proxy de aplicação | Administrador de candidatura | 
Configure propriedades do grupo de conector | Administrador de candidatura | 
Criar registo de aplicação quando a capacidade é desativada para todos os utilizadores | Desenvolvedor de aplicações | Administrador de aplicação em nuvem, administrador de aplicação
Criar grupo de conector | Administrador de candidatura | 
Eliminar o grupo de conectores | Administrador de candidatura | 
Desativar proxy de aplicações | Administrador de candidatura | 
Baixar serviço de conector | Administrador de candidatura | 
Leia todas as configurações | Administrador de candidatura | 

## <a name="b2c"></a>B2C

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Criar diretórios Azure AD B2C | Todos os utilizadores não convidados[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Criar aplicações B2C | Administrador Global | 
Criar aplicações empresariais | Administrador de Aplicações na Cloud | Administrador da Aplicação
Criar, ler, atualizar e eliminar políticas B2C | Administrador de Política B2C IEF | 
Criar, ler, atualizar e eliminar fornecedores de identidade | Administrador de Fornecedor de Identidade Externa | 
Criar, ler, atualizar e eliminar fluxos de utilizadores de redefinição de palavras-passe | Administrador de fluxo de utilizador B2C | 
Criar, ler, atualizar e eliminar fluxos de utilizadores de edição de perfis | Administrador de fluxo de utilizador B2C | 
Criar, ler, atualizar e eliminar fluxos de utilizador de entrada | Administrador de fluxo de utilizador B2C | 
Criar, ler, atualizar e eliminar o fluxo de utilizador de inscrição |Administrador de fluxo de utilizador B2C | 
Criar, ler, atualizar e eliminar atributos do utilizador | Administrador de atributo de fluxo de utilizador B2C | 
Criar, ler, atualizar e eliminar utilizadores | Administrador de Utilizadores
Leia todas as configurações | Leitor global | 
Ler registos de auditoria B2C | Leitor global[(ver documentação)](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs) | 

> [!NOTE]
> Os leitores da Azure AD B2C Global não têm as mesmas permissões que os administradores globais da Azure AD. Se tiver privilégios de administrador global Azure AD B2C, certifique-se de que está num diretório Azure AD B2C e não num diretório Azure AD.

## <a name="company-branding"></a>Imagem corporativa

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configurar o branding da empresa | Administrador Global | 
Leia todas as configurações | Leitores de diretório | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)

## <a name="company-properties"></a>Propriedades da empresa

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configurar propriedades da empresa | Administrador Global | 

## <a name="connect"></a>Ligar

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Autenticação de passagem | Administrador de Identidade Híbrida | 
Leia todas as configurações | Leitor global | Administrador de Identidade Híbrida |
Inscrição única sem emenda | Administrador de Identidade Híbrida | 

## <a name="connect-health"></a>Connect Health

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Adicionar ou eliminar serviços | Proprietário[(ver documentação)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations) | 
Aplicar correções para sincronizar erro | Contribuinte[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Proprietário
Configurar notificações | Contribuinte[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Proprietário
Configurar definições | Proprietário[(ver documentação)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations) | 
Configurar notificações de sincronização | Contribuinte[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Proprietário
Ler relatórios de segurança da ADFS | Leitor de Segurança | Contribuinte, Proprietário
Leia todas as configurações | Leitor[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Contribuinte, Proprietário
Ler erros de sincronização | Leitor[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Contribuinte, Proprietário
Ler serviços de sincronização | Leitor[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Contribuinte, Proprietário
Ver métricas e alertas | Leitor[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Contribuinte, Proprietário
Ver métricas e alertas | Leitor[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Contribuinte, Proprietário
Ver métricas e alertas de serviço sincronizado | Leitor[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Contribuinte, Proprietário

## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Gerir domínios | Administrador Global | 
Leia todas as configurações | Leitores de diretório | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)

## <a name="domain-services"></a>Serviços de Domínio

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Criar a instância de Serviços de Domínio Azure AD | Administrador Global | 
Execute todas as tarefas de Serviços de Domínio Azure AD | Grupo de administradores da AD DC Azure[(ver documentação)](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain) | 
Leia todas as configurações | Leitor na subscrição do Azure contendo serviço AD DS | 

## <a name="devices"></a>Dispositivos

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Dispositivo de desativação | Administrador de dispositivos de nuvem | 
Ativar dispositivo | Administrador de dispositivos de nuvem | 
Ler configuração básica | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Ler teclas BitLocker | Leitor de Segurança | Administrador de passwords, administrador de segurança

## <a name="enterprise-applications"></a>Aplicações Empresariais

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Consentimento para quaisquer permissões delegadas | Administrador de aplicação em nuvem | Administrador de candidatura
Consentimento para permissões de aplicação que não incluem o Microsoft Graph | Administrador de aplicação em nuvem | Administrador de candidatura
Consentimento para permissões de aplicação para o Microsoft Graph | Administrador privilegiado | 
Consentimento para aplicações de acesso a dados próprios | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Criar aplicação empresarial | Administrador de aplicação em nuvem | Administrador de candidatura
Gerir procuração de aplicação | Administrador de candidatura | 
Gerir as definições do utilizador | Administrador Global | 
Leia a revisão de acesso de um grupo ou de uma app | Leitor de Segurança | Administrador de Segurança, Administrador de Utilizador
Leia todas as configurações | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Atualizar atribuições de aplicações empresariais | Proprietário de aplicação empresarial[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar os proprietários de aplicações empresariais | Proprietário de aplicação empresarial[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar propriedades de aplicações empresariais | Proprietário de aplicação empresarial[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar o provisionamento de aplicações empresariais | Proprietário de aplicação empresarial[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar o self-service da aplicação empresarial | Proprietário de aplicação empresarial[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de aplicação em nuvem, administrador de aplicações
Atualizar propriedades de inscrição única | Proprietário de aplicação empresarial[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de aplicação em nuvem, administrador de aplicações

## <a name="entitlement-management"></a>Gestão de direitos
Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Adicione recursos a um catálogo | Administrador de utilizadores | Com a gestão de direitos, pode delegar esta tarefa ao proprietário do catálogo[(ver documentação)](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)
Adicionar sites Online SharePoint para catalogar | Administrador global


## <a name="groups"></a>Grupos

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Atribuir licença | Administrador de utilizadores | 
Criar grupos | Administrador de utilizadores | 
Criar, atualizar ou excluir a revisão de acesso de um grupo ou de uma aplicação | Administrador de utilizadores | 
Gerir a expiração do grupo | Administrador de utilizadores | 
Gerir definições do grupo | Administrador de Grupos | Administrador de Utilizadores | 
Ler todas as configurações (exceto membros ocultos) | Leitores de diretório | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)
Ler adesão escondida | Membro do grupo | Proprietário do grupo, administrador de passwords, administrador de troca, administrador do SharePoint, administrador de equipas, administrador de utilizador
Leia a adesão a grupos com adesão oculta | Administrador de Helpdesk | Administrador de utilizadores, administrador de equipas
Revogar licença | Administrador de licença | Administrador de utilizadores
Atualizar a adesão ao grupo | Proprietário do grupo[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de utilizadores
Atualizar os proprietários do grupo | Proprietário do grupo[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de utilizadores
Atualizar propriedades do grupo | Proprietário do grupo[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrador de utilizadores

## <a name="identity-protection"></a>Identity Protection

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configurar notificações de alerta| Administrador de Segurança | 
Configure e ative ou desative a política de MFA| Administrador de Segurança | 
Configure e ative ou desative a política de risco de entrada| Administrador de Segurança | 
Configure e ative ou desative a política de risco do utilizador | Administrador de Segurança | 
Configurar digestões semanais | Administrador de Segurança| 
Descartar todas as deteções de risco | Administrador de Segurança | 
Corrigir ou descartar vulnerabilidade | Administrador de Segurança | 
Leia todas as configurações | Leitor de Segurança | 
Leia todas as deteções de risco | Leitor de Segurança | 
Ler vulnerabilidades | Leitor de Segurança | 

## <a name="licenses"></a>Licenças

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Atribuir licença | Administrador de licença | Administrador de utilizadores
Leia todas as configurações | Leitores de diretório | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)
Revogar licença | Administrador de licença | Administrador de utilizadores
Tente ou compre subscrição | Administrador de faturação | 


## <a name="monitoring---audit-logs"></a>Monitorização - Registos de auditoria

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Ler registos de auditoria | Leitor de relatórios | Leitor de Segurança, Administrador de Segurança

## <a name="monitoring---sign-ins"></a>Monitorização - Inscrições

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Ler registos de login | Leitor de relatórios | Leitor de Segurança, Administrador de Segurança

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Eliminar todas as palavras-passe de aplicações existentes geradas pelos utilizadores selecionados | Administrador Global | 
Desativar o MFA | Administrador Global | 
Ativar MFA | Administrador Global | 
Gerir as definições de serviço mfa | Administrador Global | 
Exigir que os utilizadores selecionados forneçam novamente métodos de contacto | Administrador de Autenticação | 
Restaurar a autenticação de vários fatores em todos os dispositivos recordados  | Administrador de Autenticação | 

## <a name="mfa-server"></a>Servidor MFA

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Bloquear/desbloquear utilizadores | Administrador Global | 
Configurar o bloqueio da conta | Administrador Global | 
Regras de cache configuração | Administrador Global | 
Configurar alerta de fraude | Administrador Global
Configurar notificações | Administrador Global | 
Configurar um bypass único | Administrador Global | 
Configurar as definições de chamada telefónica | Administrador Global | 
Configure os fornecedores | Administrador Global | 
Configurar as definições do servidor | Administrador Global | 
Ler relatório de atividade | Leitor global | 
Leia todas as configurações | Leitor global | 
Ler estado do servidor | Leitor global |  

## <a name="organizational-relationships"></a>Relações organizacionais

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Gerir fornecedores de identidade | Administrador de Fornecedor de Identidade Externa | 
Gerir definições | Administrador Global | 
Gerir termos de utilização | Administrador Global | 
Leia todas as configurações | Leitor global | 

## <a name="password-reset"></a>Reposição de palavras-passe

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Métodos de autenticação configurar | Administrador Global |
Configurar a personalização | Administrador Global |
Configurar a notificação | Administrador Global |
Configurar a integração no local | Administrador Global |
Configurar propriedades de reset de palavra-passe | Administrador de Utilizadores | Administrador Global
Configurar o registo | Administrador Global |
Leia todas as configurações | Administrador de Segurança | Administrador de Utilizadores |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Atribuir utilizadores a funções | Administrador privilegiado | 
Configurar as definições de função | Administrador privilegiado | 
Ver atividade de auditoria | Leitor de segurança | 
Ver membros de papéis | Leitor de segurança | 

## <a name="roles-and-administrators"></a>Funções e administradores

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Gerir atribuições de funções | Administrador privilegiado | 
Leia a revisão de acesso de um papel da AD Azure  | Leitor de Segurança | Administrador de segurança, administrador privilegiado
Leia todas as configurações | Função de utilizador padrão[(ver documentação)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 

## <a name="security---authentication-methods"></a>Segurança - Métodos de autenticação

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Métodos de autenticação configurar | Administrador Global | 
Leia todas as configurações | Leitor global | 

## <a name="security---conditional-access"></a>Segurança - Acesso Condicional

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Configure endereços IP fidedignos do MFA | Administrador de Acesso Condicional | 
Criar controlos personalizados | Administrador de Acesso Condicional | Administrador de segurança
Criar locais nomeados | Administrador de Acesso Condicional | Administrador de segurança
Criar políticas | Administrador de Acesso Condicional | Administrador de segurança
Criar termos de utilização | Administrador de Acesso Condicional | Administrador de segurança
Criar certificado de conectividade VPN | Administrador de Acesso Condicional | Administrador de segurança
Eliminar a política clássica | Administrador de Acesso Condicional | Administrador de segurança
Eliminar termos de utilização | Administrador de Acesso Condicional | Administrador de segurança
Eliminar certificado de conectividade VPN | Administrador de Acesso Condicional | Administrador de segurança
Desativar a política clássica | Administrador de Acesso Condicional | Administrador de segurança
Gerir controlos personalizados | Administrador de Acesso Condicional | Administrador de segurança
Gerir locais nomeados | Administrador de Acesso Condicional | Administrador de segurança
Gerir termos de utilização | Administrador de Acesso Condicional | Administrador de segurança
Leia todas as configurações | Leitor de segurança | Administrador de segurança
Ler locais nomeados | Leitor de segurança | Administrador de Acesso Condicional, administrador de segurança

## <a name="security---identity-security-score"></a>Segurança - Pontuação de segurança de identidade

Tarefa | Papel menos privilegiado | Funções adicionais | 
---- | --------------------- | ----------------
Leia todas as configurações | Leitor de segurança | Administrador de segurança
Leia a pontuação de segurança | Leitor de segurança | Administrador de segurança
Atualizar o estado do evento | Administrador de segurança | 

## <a name="security---risky-sign-ins"></a>Segurança - Inscrições arriscadas

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Leia todas as configurações | Leitor de Segurança | 
Ler inscrições arriscadas | Leitor de Segurança | 

## <a name="security---users-flagged-for-risk"></a>Segurança - Utilizadores sinalizados para risco

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Dispensar todos os eventos | Administrador de Segurança | 
Leia todas as configurações | Leitor de Segurança | 
Ler utilizadores sinalizados para o risco | Leitor de Segurança | 

## <a name="users"></a>Utilizadores

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Adicionar o utilizador ao papel de diretório | Administrador privilegiado | 
Adicionar utilizador ao grupo | Administrador de utilizadores | 
Atribuir licença | Administrador de licença | Administrador de utilizadores
Criar o utilizador convidado | Convidado convidado | Administrador de utilizadores
Criar utilizador | Administrador de utilizadores | 
Eliminar utilizadores | Administrador de utilizadores | 
Invalidar fichas de atualização de administradores limitados (ver documentação) | Administrador de utilizadores | 
Invalidar fichas de atualização de não administradores (ver documentação) | Administrador de palavras-passe | Administrador de utilizadores
Invalidar fichas de atualização de administradores privilegiados (ver documentação) | Administrador de Autenticação Privilegiada | 
Ler configuração básica | Função de utilizador padrão[(ver documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Redefinir a palavra-passe para administradores limitados (ver documentação) | Administrador de utilizadores | 
Repor a palavra-passe de não administradores (ver documentação) | Administrador de palavras-passe | Administrador de utilizadores
Redefinir palavra-passe de administradores privilegiados | Administrador de Autenticação Privilegiada | 
Revogar licença | Administrador de licença | Administrador de utilizadores
Atualizar todas as propriedades, exceto Nome Principal do Utilizador | Administrador de utilizadores | 
Atualizar nome principal do utilizador para administradores limitados (ver documentação) | Administrador de utilizadores | 
Atualizar propriedade nome principal do utilizador em administradores privilegiados (ver documentação) | Administrador Global | 
Atualizar as definições do utilizador | Administrador Global | 


## <a name="support"></a>Suporte

Tarefa | Papel menos privilegiado | Funções adicionais
---- | --------------------- | ----------------
Enviar bilhete de apoio | Administrador de Serviços | Administrador de aplicação, Administrador de Proteção de Informação Azure, Administrador de Faturação, Administrador de Aplicação em Nuvem, Administrador de Conformidade, Administrador da Dynamics 365, Administrador de Análise de Desktop, Administrador de Troca, Administrador de Passwords, Administrador intune, Skype para Administrador de Negócios, Administrador de Energia, Administrador de Autenticação Privilegiada, Administrador de Dados, Administrador de Comunicações de Equipas, Administrador de Equipas, Administrador de Utilizador, Administrador de Análise de Local de Trabalho

## <a name="next-steps"></a>Passos seguintes

* [Como atribuir ou remover funções de administrador da AD azure](directory-manage-roles-portal.md)
* [Referência de funções de administrador da AD Azure](directory-assign-admin-roles.md)
