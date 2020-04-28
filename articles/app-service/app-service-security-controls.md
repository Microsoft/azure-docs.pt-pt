---
title: Controlos de segurança
description: Encontre uma lista de controlos de segurança para avaliar o Serviço de Aplicações Azure para a sua organização.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74671440"
---
# <a name="security-controls-for-azure-app-service"></a>Controlos de segurança para o Serviço de Aplicações Azure

Este artigo documenta os controlos de segurança incorporados no Serviço de Aplicações Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte final de serviço| Sim | Disponível para Serviço de Aplicações.| [Restrições de acesso ao serviço de aplicações Azure](app-service-ip-restrictions.md)
| Suporte à injeção VNet| Sim | Os Ambientes de Serviço de Aplicações são implementações privadas do App Service dedicadoa a um único cliente injetado na rede virtual de um cliente. | [Introdução aos Ambientes de Serviço de Aplicações](environment/intro.md)
| Suporte de isolamento de rede e firewalling| Sim | Para a variação pública multi-inquilino do App Service, os clientes podem configurar acLs de rede (restrições IP) para bloquear o tráfego de entrada permitido.  Os Ambientes de Serviço de Aplicações são implantados diretamente em redes virtuais e, portanto, podem ser protegidos com NSGs. | [Restrições de acesso ao serviço de aplicações Azure](app-service-ip-restrictions.md)
| Apoio de túnel forçado| Sim | Os ambientes de serviço de aplicações podem ser implantados na rede virtual de um cliente onde o túnel forçado é configurado. | [Configurar o Ambiente de Serviço de Aplicações com túnel forçado](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | O Serviço de Aplicações integra-se com insights de aplicação para idiomas que suportam insights de aplicação (Full .NET Framework, .NET Core, Java e Node.JS).  Consulte o desempenho do [Monitor Azure App Service](../azure-monitor/app/azure-web-apps.md). O Serviço de Aplicações também envia métricas de aplicação para o Monitor Azure. | [Monitorize aplicações no Serviço de Aplicações Azure](web-sites-monitor.md)
| Registo e auditoria de planos de controlo e gestão| Sim | Todas as operações de gestão realizadas em objetos do Serviço de Aplicações ocorrem através do Gestor de [Recursos Azure.](../azure-resource-manager/index.yml) Os registos históricos destas operações estão disponíveis tanto no portal como através do CLI. | [Operações](../role-based-access-control/resource-provider-operations.md#microsoftweb)de fornecedor de recursos do Gestor de Recursos Azure , [az monitor de atividade-log](/cli/azure/monitor/activity-log)
| Registo e auditoria de planos de dados | Não | O plano de dados do Serviço de Aplicações é uma partilha remota de ficheiros contendo o conteúdo do site implantado por um cliente.  Não existe auditoria da parte remota do ficheiro. |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas |  Documentação
|---|---|--|
| Autenticação| Sim | Os clientes podem construir aplicações no Serviço de Aplicações que se integrem automaticamente com o [Azure Ative Directory (Azure AD),](../active-directory/index.yml) bem como outros fornecedores de identidade compatíveis com a OAuth Para acesso à gestão aos ativos do App Service, todo o acesso é controlado por uma combinação de funções de diretor audato azure AD e DoT DBAC do Gestor de Recursos Azure. | [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md)
| Autorização| Sim | Para acesso à gestão aos ativos do Serviço app, todo o acesso é controlado por uma combinação de funções de RBAC autenticados da Azure AD e do Gestor de Recursos Azure.  | [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | O conteúdo do ficheiro do site é armazenado no Armazenamento Azure, que encripta automaticamente o conteúdo em repouso. <br><br>Os segredos fornecidos pelo cliente são encriptados em repouso. Os segredos são encriptados em repouso enquanto armazenados nas bases de dados de configuração do Serviço de Aplicações.<br><br>Os discos ligados localmente podem ser utilizados opcionalmente como armazenamento temporário por websites (D:\local e %TMP%). Os discos ligados localmente não são encriptados em repouso. | [Encriptação azure storage para dados em repouso](../storage/common/storage-service-encryption.md)
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim | Os clientes podem optar por armazenar segredos de aplicação no Key Vault e recuperá-los em tempo de funcionamento. | [Utilize referências chave vault para serviço de aplicações e funções azure (pré-visualização)](app-service-key-vault-references.md)
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | Os clientes podem configurar web sites para exigir e usar HTTPS para tráfego de entrada.  | [Como fazer um Serviço de Aplicações Azure HTTPS apenas](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (post de blog)
| Chamadas api encriptadas| Sim | As chamadas de gestão para configurar o Serviço de Aplicações ocorrem através de chamadas do [Gestor de Recursos Do Azure](../azure-resource-manager/index.yml) através de HTTPS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | Para operações de gestão, o estado de uma configuração do Serviço de Aplicações pode ser exportado como um modelo de Gestor de Recursos Azure e versão ao longo do tempo. Para operações de tempo de execução, os clientes podem manter várias versões ao vivo diferentes de uma aplicação utilizando a funcionalidade de slots de implementação do Serviço de Aplicações. | 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)
