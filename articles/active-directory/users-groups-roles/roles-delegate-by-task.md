---
title: Delegar funções com privilégios mínimos pela tarefa de administrador - Azure Active Directory | Documentos da Microsoft
description: Funções para delegar para tarefas de identidade no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59c06ae83327683942885190e4b401617dc020f9
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361375"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Funções de administrador pela tarefa de administração no Azure Active Directory

Neste artigo, pode encontrar as informações necessárias para restringir permissões de administrador de um utilizador através da atribuição de funções com privilégios mínimos no Azure Active Directory (Azure AD). Encontrará as tarefas de administrador organizadas por área de recursos e a função com privilégios mínimos necessários para executar cada tarefa, juntamente com as funções adicionais não - Administrador Global, que pode realizar a tarefa.

## <a name="application-proxy"></a>Proxy da aplicação

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar a aplicação de proxy de aplicação | Administrador de aplicações | 
Configurar propriedades de grupo do conector | Administrador de aplicações | 
Criar registo de aplicação quando a capacidade está desabilitada para todos os utilizadores | Programador de aplicações | Administrador da aplicação na cloud, administrador de aplicações
Criar grupo do conector | Administrador de aplicações | 
Eliminar grupo de conectores | Administrador de aplicações | 
Desativar proxy de aplicações | Administrador de aplicações | 
Transferir o serviço de conector | Administrador de aplicações | 
Leia toda a configuração | Administrador de aplicações | 

## <a name="b2c"></a>B2C

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Criar diretórios do Azure AD B2C | Todos os utilizadores não-convidado ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Criar aplicações B2C | Administrador Global | 
Criar aplicações empresariais | Administrador da Aplicação Cloud | Administrador de Aplicações
Criar, ler, atualizar e eliminar políticas de B2C | Administrador Global | 
Criar, ler, atualizar e eliminar os fornecedores de identidade | Administrador Global | 
Criar, ler, atualizar e eliminar fluxos de utilizador de reposição de palavra-passe | Administrador Global | 
Criar, ler, atualizar e eliminar fluxos de utilizador de edição de perfil | Administrador Global | 
Criar, ler, atualizar e eliminar fluxos de utilizador de início de sessão | Administrador Global | 
Criar, ler, atualizar e eliminar o fluxo de inscrição de utilizador |Administrador Global | 
Criar, ler, atualizar e eliminar os atributos de utilizador | Administrador Global | 
Criar, ler, atualizar e eliminar utilizadores | Administrador global ([consulte a documentação](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Leia toda a configuração | Administrador Global | 
Registos de auditoria de B2C de leitura | Administrador global ([consulte a documentação](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

## <a name="company-branding"></a>Imagem corporativa

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar o branding da empresa | Administrador Global | 
Leia toda a configuração | Leitores de diretórios | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Propriedades da empresa

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar propriedades de empresa | Administrador Global | 

## <a name="connect"></a>Ligar

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Autenticação pass-through | Administrador Global | 
Leia toda a configuração | Administrador Global | 
Início de sessão único totalmente integrado | Administrador Global | 

## <a name="connect-health"></a>Connect Health

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Adicionar ou eliminar serviços | Proprietário ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Aplicar correções a um erro de sincronização | Contribuidor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietário
Configurar notificações | Contribuidor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietário
Configurar definições | Proprietário ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Configurar notificações de sincronização | Contribuidor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietário
Relatórios de segurança do AD FS de leitura | Leitor de Segurança | Contribuidor, proprietário
Leia toda a configuração | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Contribuidor, proprietário
Erros de sincronização de leitura | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Contribuidor, proprietário
Serviços de sincronização de leitura | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Contribuidor, proprietário
Ver métricas e alertas | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Contribuidor, proprietário
Ver métricas e alertas | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Contribuidor, proprietário
Ver métricas de serviço de sincronização e alertas | Leitor ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Contribuidor, proprietário


## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Gerir domínios | Administrador Global | 
Leia toda a configuração | Leitores de diretórios | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Serviços de Domínio

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Criar instância do Azure AD Domain Services | Administrador Global | 
Executar todas as tarefas do Azure AD Domain Services | Grupo de administradores de controlador de domínio do AD do Azure ([consulte a documentação](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Leia toda a configuração | Leitor de subscrição do Azure que contém o serviço AD DS | 

## <a name="devices"></a>Dispositivos

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Desativar dispositivo | Administrador de dispositivo na cloud | 
Ativar dispositivo | Administrador de dispositivo na cloud | 
Configuração básica de leitura | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Chaves do BitLocker de leitura | Leitor de Segurança | Administrador de palavras-passe, o administrador de segurança

## <a name="enterprise-applications"></a>Aplicações Empresariais

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Dar consentimento a permissões delegadas | Administrador da aplicação Cloud | Administrador de aplicações
Dar consentimento a permissões de aplicação não incluindo o Microsoft Graph ou Graph do Azure AD | Administrador da aplicação Cloud | Administrador de aplicações
Dar consentimento a permissões de aplicação para o Microsoft Graph ou Graph do Azure AD | Administrador Global | 
Autorizar aplicações aceder aos dados próprios | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Criar aplicação empresarial | Administrador da aplicação Cloud | Administrador de aplicações
Gerir o Proxy de aplicações | Administrador de aplicações | 
Gerir definições de utilizador | Administrador Global | 
Revisão de acesso de leitura de um grupo ou de uma aplicação | Leitor de Segurança | Administrador de segurança, administrador de utilizadores
Leia toda a configuração | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Atualizar as atribuições de aplicações empresariais | Proprietário da aplicação empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador da aplicação na cloud, administrador de aplicações
Atualizar os proprietários da aplicação empresarial | Proprietário da aplicação empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador da aplicação na cloud, administrador de aplicações
Atualizar propriedades da aplicação empresarial | Proprietário da aplicação empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador da aplicação na cloud, administrador de aplicações
Atualizar o provisionamento de aplicativos empresariais | Proprietário da aplicação empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador da aplicação na cloud, administrador de aplicações
Atualizar aplicação empresarial self-service | Proprietário da aplicação empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador da aplicação na cloud, administrador de aplicações
Atualizar as propriedades de início de sessão únicas | Proprietário da aplicação empresarial ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador da aplicação na cloud, administrador de aplicações

## <a name="groups"></a>Grupos

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Atribuir licenças | Administrador de utilizadores | 
Criar grupo | Administrador de utilizadores | 
Criar, atualizar ou eliminar a revisão de acesso de um grupo ou de uma aplicação | Administrador de utilizadores | 
Gerir a expiração de grupo | Administrador de utilizadores | 
Gerir definições do grupo | Administrador Global | 
Leia toda a configuração (exceto associação oculta) | Leitores de diretórios | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Associação de leitura, ocultada | Membro do grupo | O proprietário do grupo, o administrador de palavras-passe, administrador do Exchange, administrador do SharePoint, o administrador de Equipes, administrador de utilizadores
Leia a associação de grupos com associação oculta a | Administrador de Suporte Técnico | Administrador de utilizador, administrador de Equipes
Revogar a licença | Administrador de licença | Administrador de utilizadores
Atualizar associação de grupo | O proprietário do grupo ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de utilizadores
Proprietários do grupo de atualização | O proprietário do grupo ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de utilizadores
Propriedades do grupo de atualização | O proprietário do grupo ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Administrador de utilizadores

## <a name="identity-protection"></a>Identity Protection

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar notificações de alerta| Administrador de Segurança | 
Configurar e ativar ou desativar a política da MFA| Administrador de Segurança | 
Configurar e ativar ou desativar a política de risco de início de sessão| Administrador de Segurança | 
Configurar e ativar ou desativar a política de risco do utilizador | Administrador de Segurança | 
Configurar os resumos de semanais | Administrador de Segurança| 
Dispensar todos os eventos de risco | Administrador de Segurança | 
Corrigir ou dispensar vulnerabilidade | Administrador de Segurança | 
Leia toda a configuração | Leitor de Segurança | 
Ler todos os eventos de risco | Leitor de Segurança | 
Vulnerabilidades de leitura | Leitor de Segurança | 

## <a name="licenses"></a>Licenças

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Atribuir licenças | Administrador de licença | Administrador de utilizadores
Leia toda a configuração | Leitores de diretórios | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Revogar a licença | Administrador de licença | Administrador de utilizadores
Experimente ou adquira subscrição | Administrador de faturação | 


## <a name="monitoring---audit-logs"></a>Monitorização – registos de auditoria

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Registos de auditoria de leitura | Leitor de relatórios | Leitor de segurança, administrador de segurança

## <a name="monitoring---sign-ins"></a>Monitorização - inícios de sessão

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Ler os registos de início de sessão | Leitor de relatórios | Leitor de segurança, administrador de segurança

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Eliminar todas as palavras-passe de aplicação existentes geradas pelos utilizadores selecionados | Administrador Global | 
Desativar a MFA | Administrador Global | 
Ativar MFA | Administrador Global | 
Gerir definições do serviço MFA | Administrador Global | 
Exigir que os utilizadores selecionados forneçam novamente os métodos de contacto | Administrador da Autenticação | 
Restaurar a autenticação multifator em todos os dispositivos memorizados  | Administrador da Autenticação | 

## <a name="mfa-server"></a>Servidor MFA

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Bloquear/desbloquear utilizadores | Administrador Global | 
Configurar o bloqueio de conta | Administrador Global | 
Configurar regras de colocação em cache | Administrador Global | 
Configurar o alerta de fraude | Administrador Global
Configurar notificações | Administrador Global | 
Configurar a omissão de uso individual | Administrador Global | 
Configurar as definições de chamada telefónica | Administrador Global | 
Configurar fornecedores | Administrador Global | 
Configurar definições do servidor | Administrador Global | 
Relatório de atividade de leitura | Administrador Global | 
Leia toda a configuração | Administrador Global | 
Ler estado do servidor | Administrador Global |  

## <a name="organizational-relationships"></a>Relações organizacionais

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Gerir fornecedores de identidade | Administrador Global | 
Gerir definições | Administrador Global | 
Gerir termos de utilização | Administrador Global | 
Leia toda a configuração | Administrador Global | 

## <a name="password-reset"></a>Reposição de palavras-passe

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar métodos de autenticação | Administrador Global |
Configurar a personalização | Administrador Global |
Configurar notificação | Administrador Global |
Configurar a integração no local | Administrador Global |
Configurar propriedades de reposição de palavra-passe | Administrador de Utilizadores | Administrador Global
Configurar registo | Administrador Global |
Leia toda a configuração | Administrador de Segurança | Administrador de Utilizadores |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Atribuir utilizadores a funções | Administrador com Função Privilegiada | 
Configurar as definições de função | Administrador com Função Privilegiada | 
Ver a atividade de auditoria | Leitor de segurança | 
Associações de função do Vista | Leitor de segurança | 

## <a name="roles-and-administrators"></a>Funções e administradores

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Gerir atribuições de funções | Administrador com Função Privilegiada | 
Revisão de acesso de leitura de uma função do Azure AD  | Leitor de Segurança | Administrador de segurança, o administrador com função privilegiada
Leia toda a configuração | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Segurança - métodos de autenticação

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar métodos de autenticação | Administrador Global | 
Leia toda a configuração | Administrador Global | 

## <a name="security---conditional-access"></a>Security - acesso condicional

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Configurar a MFA fidedigno endereços IP | Administrador de acesso condicional | 
Criar controles personalizados | Administrador de acesso condicional | Administrador de segurança
Criar localizações com nome | Administrador de acesso condicional | Administrador de segurança
Criar políticas | Administrador de acesso condicional | Administrador de segurança
Criar termos de utilização | Administrador de acesso condicional | Administrador de segurança
Criar certificado de conectividade VPN | Administrador de acesso condicional | Administrador de segurança
Eliminar política clássica | Administrador de acesso condicional | Administrador de segurança
Eliminar termos de utilização | Administrador de acesso condicional | Administrador de segurança
Eliminar certificado de conectividade VPN | Administrador de acesso condicional | Administrador de segurança
Desativar política clássica | Administrador de acesso condicional | Administrador de segurança
Gerir os controlos personalizados | Administrador de acesso condicional | Administrador de segurança
Gerir localizações com nome | Administrador de acesso condicional | Administrador de segurança
Gerir termos de utilização | Administrador de acesso condicional | Administrador de segurança
Leia toda a configuração | Leitor de segurança | Administrador de segurança
Localizações com nome de leitura | Leitor de segurança | Administrador de acesso condicional, o administrador de segurança

## <a name="security---identity-security-score"></a>Segurança - pontuação de segurança de identidade

Tarefa | Função com privilégios mínimos | Funções adicionais | 
---- | --------------------- | ----------------
Leia toda a configuração | Leitor de segurança | Administrador de segurança
Pontuação de segurança de leitura | Leitor de segurança | Administrador de segurança
Atualizar estado do evento | Administrador de segurança | 

## <a name="security---risky-sign-ins"></a>Segurança - inícios de sessão arriscados

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Leia toda a configuração | Leitor de Segurança | 
Leia os inícios de sessão arriscados | Leitor de Segurança | 

## <a name="security---users-flagged-for-risk"></a>Segurança - utilizadores sinalizados para risco

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Dispensar todos os eventos | Administrador de Segurança | 
Leia toda a configuração | Leitor de Segurança | 
Utilizadores sinalizados para risco de leitura | Leitor de Segurança | 

## <a name="users"></a>Utilizadores

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Adicionar utilizador à função de diretório | Administrador com Função Privilegiada | 
Adicionar utilizador ao grupo | Administrador de utilizadores | 
Atribuir licenças | Administrador de licença | Administrador de utilizadores
Criar utilizador convidado | Autor de convites | Administrador de utilizadores
Criar utilizador | Administrador de utilizadores | 
Eliminar utilizadores | Administrador de utilizadores | 
Invalidar os tokens de atualização de administradores limitados (consulte a documentação) | Administrador de utilizadores | 
Invalidar os tokens de atualização de não-administradores (consulte a documentação) | Administrador de palavras-passe | Administrador de utilizadores
Invalidar os tokens de atualização de administradores com privilégios (consulte a documentação) | Administrador Global | 
Configuração básica de leitura | Função de utilizador padrão ([consulte a documentação](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Repor palavra-passe para administradores limitados (consulte a documentação) | Administrador de utilizadores | 
Repor palavra-passe de não-administradores (consulte a documentação) | Administrador de palavras-passe | Administrador de utilizadores
Repor palavra-passe de administradores com privilégios | Administrador Global | 
Revogar a licença | Administrador de licença | Administrador de utilizadores
Atualizar todas as propriedades, exceto o nome Principal de utilizador | Administrador de utilizadores | 
Atualizar o nome do Principal de utilizador para administradores limitados (consulte a documentação) | Administrador de utilizadores | 
Atualize a propriedade de nome Principal de utilizador em administradores com privilégios (consulte a documentação) | Administrador Global | 
Atualizar as definições de utilizador | Administrador Global | 


## <a name="support"></a>Suporte

Tarefa | Função com privilégios mínimos | Funções adicionais
---- | --------------------- | ----------------
Submeter pedido de suporte | Administrador de Serviços | Administrador da aplicação, de faturação administrador, o administrador da aplicação na Cloud, o administrador de conformidade, o administrador do Dynamics 365, o administrador de análise de ambiente de trabalho, o administrador do Exchange, o palavra-passe de administrador, proteção de informações Administrador, administrador do Intune, o Skype para o administrador da empresa, administrador do Power BI, com privilégios de autenticação de administrador, administrador do SharePoint, administrador de comunicações de Equipes, as Equipes de administrador, administrador de utilizadores, Administrador de análise de área de trabalho

## <a name="next-steps"></a>Passos Seguintes

* [Como atribuir ou remover funções de administrador do azure AD](directory-manage-roles-portal.md)
* [Referenciam de funções de administrador do Azure AD](directory-assign-admin-roles.md)
