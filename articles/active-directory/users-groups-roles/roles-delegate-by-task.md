---
title: Delegar funções menos privilegiadas por tarefa do administrador-Azure Active Directory | Microsoft Docs
description: Funções a delegar para tarefas de identidade no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5933fdd9932bd8c13ab0f231d677ce06112662ed
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169762"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Funções de administrador por tarefa de administrador no Azure Active Directory

Neste artigo, você pode encontrar as informações necessárias para restringir as permissões de administrador de um usuário atribuindo funções menos privilegiadas no Azure Active Directory (Azure AD). Você encontrará as tarefas do administrador organizadas por área de recursos e a função menos privilegiada necessária para executar cada tarefa, juntamente com funções de administrador não globais adicionais que podem executar a tarefa.

## <a name="application-proxy"></a>Proxy da aplicação

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar aplicativo de proxy de aplicativo | Administrador de aplicativos | 
Configurar propriedades do grupo de conectores | Administrador de aplicativos | 
Criar registro de aplicativo quando a capacidade estiver desabilitada para todos os usuários | Desenvolvedor de aplicativos | Administrador de aplicativos de nuvem, administrador de aplicativos
Criar grupo de conectores | Administrador de aplicativos | 
Excluir grupo de conectores | Administrador de aplicativos | 
Desativar proxy de aplicações | Administrador de aplicativos | 
Baixar serviço do conector | Administrador de aplicativos | 
Ler todas as configurações | Administrador de aplicativos | 

## <a name="b2c"></a>B2C

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Criar diretórios Azure AD B2C | Todos os usuários não convidados ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Criar aplicativos B2C | Administrador Global | 
Criar aplicativos empresariais | Administrador de aplicativos de nuvem | Administrador de aplicativos
Criar, ler, atualizar e excluir políticas B2C | Administrador Global | 
Criar, ler, atualizar e excluir provedores de identidade | Administrador Global | 
Criar, ler, atualizar e excluir fluxos de usuário de redefinição de senha | Administrador Global | 
Criar, ler, atualizar e excluir fluxos de usuário de edição de perfil | Administrador Global | 
Criar, ler, atualizar e excluir fluxos de usuário de entrada | Administrador Global | 
Criar, ler, atualizar e excluir o fluxo de usuário de inscrição |Administrador Global | 
Criar, ler, atualizar e excluir atributos de usuário | Administrador Global | 
Criar, ler, atualizar e excluir usuários | Administrador global ([consulte a documentação](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Ler todas as configurações | Administrador Global | 
Ler logs de auditoria B2C | Administrador global ([consulte a documentação](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C administradores globais não têm as mesmas permissões que os administradores globais do Azure AD. Se você tiver Azure AD B2C privilégios de administrador global, verifique se você está em um diretório Azure AD B2C e não em um diretório do AD do Azure.

## <a name="company-branding"></a>Identidade visual da empresa

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar o branding da empresa | Administrador Global | 
Ler todas as configurações | Leitores de diretório | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Propriedades da empresa

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar propriedades da empresa | Administrador Global | 

## <a name="connect"></a>Ligar

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Autenticação de passagem | Administrador Global | 
Ler todas as configurações | Administrador Global | 
Início de sessão único totalmente integrado | Administrador Global | 

## <a name="connect-health"></a>Connect Health

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Adicionar ou excluir serviços | Proprietário ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Aplicar correções ao erro de sincronização | Colaborador ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietário
Configurar notificações | Colaborador ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietário
Configurar definições | Proprietário ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Configurar notificações de sincronização | Colaborador ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietário
Ler relatórios de segurança do ADFS | Leitor de segurança | Colaborador, proprietário
Ler todas as configurações | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Colaborador, proprietário
Ler erros de sincronização | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Colaborador, proprietário
Ler serviços de sincronização | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Colaborador, proprietário
Exibir métricas e alertas | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Colaborador, proprietário
Exibir métricas e alertas | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Colaborador, proprietário
Exibir métricas e alertas do serviço de sincronização | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Colaborador, proprietário


## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Gerenciar domínios | Administrador Global | 
Ler todas as configurações | Leitores de diretório | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Serviços de Domínio

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Criar instância de Azure AD Domain Services | Administrador Global | 
Executar todas as tarefas de Azure AD Domain Services | Grupo de administradores de DC do Azure AD ([consulte a documentação](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Ler todas as configurações | Leitor na assinatura do Azure que contém o serviço de AD DS | 

## <a name="devices"></a>Dispositivos

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Desabilitar dispositivo | Administrador de dispositivo de nuvem | 
Habilitar dispositivo | Administrador de dispositivo de nuvem | 
Ler configuração básica | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Ler chaves do BitLocker | Leitor de segurança | Administrador de senhas, administrador de segurança

## <a name="enterprise-applications"></a>Aplicações empresariais

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Consentimento para permissões delegadas | Administrador de aplicativos de nuvem | Administrador de aplicativos
Consentimento para permissões de aplicativo que não incluem Microsoft Graph ou o Azure AD Graph | Administrador de aplicativos de nuvem | Administrador de aplicativos
Consentimento para permissões de aplicativo para Microsoft Graph ou Azure AD Graph | Administrador Global | 
Consentimento para aplicativos que acessam dados próprios | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Criar aplicativo empresarial | Administrador de aplicativos de nuvem | Administrador de aplicativos
Gerenciar proxy de aplicativo | Administrador de aplicativos | 
Gerenciar configurações de usuário | Administrador Global | 
Ler a revisão de acesso de um grupo ou de um aplicativo | Leitor de segurança | Administrador de segurança, administrador de usuários
Ler todas as configurações | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Atualizar atribuições de aplicativo empresarial | Proprietário do aplicativo empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de aplicativos de nuvem, administrador de aplicativos
Atualizar proprietários de aplicativos empresariais | Proprietário do aplicativo empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de aplicativos de nuvem, administrador de aplicativos
Atualizar propriedades do aplicativo empresarial | Proprietário do aplicativo empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de aplicativos de nuvem, administrador de aplicativos
Atualizar o provisionamento de aplicativos empresariais | Proprietário do aplicativo empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de aplicativos de nuvem, administrador de aplicativos
Atualizar autoatendimento de aplicativo empresarial | Proprietário do aplicativo empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de aplicativos de nuvem, administrador de aplicativos
Atualizar propriedades de logon único | Proprietário do aplicativo empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de aplicativos de nuvem, administrador de aplicativos

## <a name="entitlement-management"></a>Gestão de direitos
Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Adicionar recursos a um catálogo | Administrador do usuário | Com o gerenciamento de direitos, você pode delegar essa tarefa para o proprietário do catálogo ([consulte a documentação](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Adicionar sites do SharePoint Online ao catálogo | Administrador global


## <a name="groups"></a>Grupos

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Atribuir licença | Administrador do usuário | 
Criar grupo | Administrador do usuário | 
Criar, atualizar ou excluir a revisão de acesso de um grupo ou de um aplicativo | Administrador do usuário | 
Gerenciar expiração do grupo | Administrador do usuário | 
Gerir definições do grupo | Administrador Global | 
Ler todas as configurações (exceto a associação oculta) | Leitores de diretório | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Ler associação oculta | Membro do grupo | Proprietário do grupo, administrador de senhas, administrador do Exchange, administrador do SharePoint, administrador de equipes, administrador de usuários
Ler Associação de grupos com associação oculta | Administrador de assistência técnica | Administrador de usuários, administrador de equipes
Revogar licença | Administrador de licenças | Administrador do usuário
Atualizar Associação de grupo | Proprietário do grupo ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador do usuário
Atualizar proprietários do grupo | Proprietário do grupo ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador do usuário
Atualizar propriedades do grupo | Proprietário do grupo ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador do usuário

## <a name="identity-protection"></a>Proteção de Identidade

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar notificações de alerta| Administrador de segurança | 
Configurar e habilitar ou desabilitar a política de MFA| Administrador de segurança | 
Configurar e habilitar ou desabilitar a política de risco de entrada| Administrador de segurança | 
Configurar e habilitar ou desabilitar a política de risco do usuário | Administrador de segurança | 
Configurar resumos semanais | Administrador de segurança| 
Ignorar todas as detecções de risco | Administrador de segurança | 
Corrigir ou ignorar vulnerabilidade | Administrador de segurança | 
Ler todas as configurações | Leitor de segurança | 
Ler todas as detecções de riscos | Leitor de segurança | 
Vulnerabilidades de leitura | Leitor de segurança | 

## <a name="licenses"></a>Las

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Atribuir licença | Administrador de licenças | Administrador do usuário
Ler todas as configurações | Leitores de diretório | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Revogar licença | Administrador de licenças | Administrador do usuário
Experimentar ou comprar assinatura | Administrador de faturação | 


## <a name="monitoring---audit-logs"></a>Monitoramento-logs de auditoria

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Ler logs de auditoria | Leitor de relatórios | Leitor de segurança, administrador de segurança

## <a name="monitoring---sign-ins"></a>Monitoramento-entradas

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Ler logs de entrada | Leitor de relatórios | Leitor de segurança, administrador de segurança

## <a name="multi-factor-authentication"></a>Autenticação multifator

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados | Administrador Global | 
Desabilitar MFA | Administrador Global | 
Ativar MFA | Administrador Global | 
Gerenciar configurações do serviço MFA | Administrador Global | 
Exigir que os usuários selecionados forneçam métodos de contato novamente | Administrador de autenticação | 
Restaurar a autenticação multifator em todos os dispositivos lembrados  | Administrador de autenticação | 

## <a name="mfa-server"></a>Servidor MFA

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Bloquear/desbloquear usuários | Administrador Global | 
Configurar o bloqueio de conta | Administrador Global | 
Configurar regras de cache | Administrador Global | 
Configurar alerta de fraude | Administrador Global
Configurar notificações | Administrador Global | 
Configurar bypass único | Administrador Global | 
Definir configurações de chamada telefônica | Administrador Global | 
Configurar provedores | Administrador Global | 
Definir configurações do servidor | Administrador Global | 
Ler relatório de atividade | Administrador Global | 
Ler todas as configurações | Administrador Global | 
Ler status do servidor | Administrador Global |  

## <a name="organizational-relationships"></a>Relações organizacionais

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Gerenciar provedores de identidade | Administrador Global | 
Gerir definições | Administrador Global | 
Gerenciar termos de uso | Administrador Global | 
Ler todas as configurações | Administrador Global | 

## <a name="password-reset"></a>Reposição de palavras-passe

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar métodos de autenticação | Administrador Global |
Configurar personalização | Administrador Global |
Configurar notificação | Administrador Global |
Configurar a integração local | Administrador Global |
Configurar propriedades de redefinição de senha | Administrador do usuário | Administrador Global
Configurar o registro | Administrador Global |
Ler todas as configurações | Administrador de segurança | Administrador do usuário |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Atribuir usuários a funções | Administrador de função com privilégios | 
Configurar as definições de função | Administrador de função com privilégios | 
Exibir atividade de auditoria | Leitor de segurança | 
Exibir associações de função | Leitor de segurança | 

## <a name="roles-and-administrators"></a>Funções e administradores

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Gerir atribuições de funções | Administrador de função com privilégios | 
Ler a revisão de acesso de uma função do Azure AD  | Leitor de segurança | Administrador de segurança, administrador de função com privilégios
Ler todas as configurações | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Métodos de autenticação de segurança

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar métodos de autenticação | Administrador Global | 
Ler todas as configurações | Administrador Global | 

## <a name="security---conditional-access"></a>Segurança-acesso condicional

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar endereços IP confiáveis do MFA | Administrador de acesso condicional | 
Criar controles personalizados | Administrador de acesso condicional | Administrador de segurança
Criar localizações nomeadas | Administrador de acesso condicional | Administrador de segurança
Criar políticas | Administrador de acesso condicional | Administrador de segurança
Criar termos de uso | Administrador de acesso condicional | Administrador de segurança
Criar certificado de conectividade VPN | Administrador de acesso condicional | Administrador de segurança
Excluir política clássica | Administrador de acesso condicional | Administrador de segurança
Excluir termos de uso | Administrador de acesso condicional | Administrador de segurança
Excluir certificado de conectividade VPN | Administrador de acesso condicional | Administrador de segurança
Desabilitar política clássica | Administrador de acesso condicional | Administrador de segurança
Gerenciar controles personalizados | Administrador de acesso condicional | Administrador de segurança
Gerenciar locais nomeados | Administrador de acesso condicional | Administrador de segurança
Gerenciar termos de uso | Administrador de acesso condicional | Administrador de segurança
Ler todas as configurações | Leitor de segurança | Administrador de segurança
Ler locais nomeados | Leitor de segurança | Administrador de acesso condicional, administrador de segurança

## <a name="security---identity-security-score"></a>Segurança-Pontuação de segurança de identidade

Tarefa | Função com privilégios mínimos | Funções adicionais | 
---- | --------------------- | ----------------
Ler todas as configurações | Leitor de segurança | Administrador de segurança
Ler Pontuação de segurança | Leitor de segurança | Administrador de segurança
Atualizar status do evento | Administrador de segurança | 

## <a name="security---risky-sign-ins"></a>Entradas com segurança arriscada

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Ler todas as configurações | Leitor de segurança | 
Ler entradas arriscadas | Leitor de segurança | 

## <a name="security---users-flagged-for-risk"></a>Segurança-usuários sinalizados para risco

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Dispensar todos os eventos | Administrador de segurança | 
Ler todas as configurações | Leitor de segurança | 
Ler usuários sinalizados para risco | Leitor de segurança | 

## <a name="users"></a>Utilizadores

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Adicionar usuário à função de diretório | Administrador de função com privilégios | 
Adicionar usuário ao grupo | Administrador do usuário | 
Atribuir licença | Administrador de licenças | Administrador do usuário
Criar usuário convidado | Convite do convidado | Administrador do usuário
Criar usuário | Administrador do usuário | 
Excluir usuários | Administrador do usuário | 
Invalidar tokens de atualização de administradores limitados (consulte a documentação) | Administrador do usuário | 
Invalidar tokens de atualização de não-administradores (consulte a documentação) | Administrador de senha | Administrador do usuário
Invalidar tokens de atualização de administradores privilegiados (consulte a documentação) | Administrador Global | 
Ler configuração básica | Função de usuário padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Redefinir senha para administradores limitados (consulte a documentação) | Administrador do usuário | 
Redefinir a senha de não-administradores (consulte a documentação) | Administrador de senha | Administrador do usuário
Redefinir senha de administradores privilegiados | Administrador Global | 
Revogar licença | Administrador de licenças | Administrador do usuário
Atualizar todas as propriedades, exceto o nome principal do usuário | Administrador do usuário | 
Atualizar o nome principal do usuário para administradores limitados (consulte a documentação) | Administrador do usuário | 
Atualizar a propriedade nome principal do usuário em administradores privilegiados (consulte a documentação) | Administrador Global | 
Atualizar configurações do usuário | Administrador Global | 


## <a name="support"></a>Suporte

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Enviar tíquete de suporte | Administrador de Serviços | Administrador de aplicativos, administrador da proteção de informações do Azure, administrador de cobrança, administrador de aplicativos de nuvem, administrador de conformidade, administrador do Dynamics 365, administrador de análise de desktop, administrador do Exchange, senha Administrador, administrador do Intune, administrador do Skype for Business, administrador de Power BI, administrador de autenticação privilegiada, administrador do SharePoint, administrador de comunicações de equipes, administrador de equipes, administrador de usuários, Administrador do workplace Analytics

## <a name="next-steps"></a>Passos seguintes

* [Como atribuir ou remover funções de administrador do Azure AD](directory-manage-roles-portal.md)
* [Referência de funções de administrador do Azure AD](directory-assign-admin-roles.md)
