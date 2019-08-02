---
title: Gerenciamento da rede de produção do Azure-Microsoft Azure
description: Este artigo descreve como a Microsoft gerencia e opera a rede de produção do Azure para proteger os data centers do Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727121"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Gerenciamento e operação da rede de produção do Azure    
Este artigo descreve como a Microsoft gerencia e opera a rede de produção do Azure para proteger os data centers do Azure.

## <a name="monitor-log-and-report"></a>Monitorar, registrar e relatar

O gerenciamento e a operação da rede de produção do Azure são um esforço coordenado entre as equipes de operações do Azure e do banco de dados SQL do Azure. As equipes usam várias ferramentas de monitoramento de desempenho do sistema e do aplicativo no ambiente. E usam as ferramentas apropriadas para monitorar dispositivos de rede, servidores, serviços e processos de aplicativos.

Para garantir a execução segura de serviços em execução no ambiente do Azure, as equipes de operações implementam vários níveis de monitoramento, registro em log e relatórios, incluindo as seguintes ações:

- Basicamente, o Microsoft Monitoring Agent (MMA) coleta informações de log e diagnósticos de vários locais, incluindo o controlador de malha (FC) e o sistema operacional raiz (SO) e grava-os nos arquivos de log. O agente, por fim, envia por push um subconjunto resumido das informações para uma conta de armazenamento do Azure pré-configurada. Além disso, o serviço de monitoramento e diagnóstico independente lê vários dados de log de monitoramento e diagnóstico e resume as informações. O serviço de monitoramento e diagnóstico grava as informações em um log integrado. O Azure usa o monitoramento de segurança personalizado do Azure, que é uma extensão para o sistema de monitoramento do Azure. Ele tem componentes que observam, analisam e relatam eventos pertinentes à segurança de vários pontos na plataforma.

- A plataforma de Windows Fabric do banco de dados SQL do Azure fornece serviços de gerenciamento, implantação, desenvolvimento e supervisão operacional para o banco de dados SQL do Azure. A plataforma oferece serviços de implantação de várias etapas distribuídos, monitoramento de integridade, reparos automáticos e conformidade de versão de serviço. Ele fornece os seguintes serviços:

   - Recursos de modelagem de serviço com ambiente de desenvolvimento de alta fidelidade (os clusters de datacenter são caros e escassos).
   - Fluxos de trabalho de implantação e atualização com um clique para a inicialização e a manutenção do serviço.
   - Relatórios de integridade com fluxos de trabalho de reparo automatizados para habilitar a auto-recuperação.
   - Monitoramento em tempo real, alertas e recursos de depuração em todos os nós de um sistema distribuído.
   - Coleção centralizada de dados operacionais e métricas para análise de causa raiz distribuída e Service insights.
   - Ferramentas operacionais para implantação, gerenciamento de alterações e monitoramento.
   - O banco de dados SQL do Azure Windows Fabric a plataforma e os scripts de Watchdog são executados continuamente e monitorados em tempo real.

Se ocorrerem anomalias, o processo de resposta a incidentes seguido pela equipe de triagem de incidentes do Azure será ativado. A equipe de suporte do Azure apropriada é notificada para responder ao incidente. O rastreamento de problemas e a resolução são documentados e gerenciados em um sistema de tíquetes centralizado. As métricas de tempo de atividade do sistema estão disponíveis no acordo de não divulgação (NDA) e mediante solicitação.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rede corporativa e acesso multifator à produção
A base de usuários da rede corporativa inclui a equipe de suporte do Azure. A rede corporativa dá suporte a funções corporativas internas e inclui acesso a aplicativos internos que são usados para o suporte ao cliente do Azure. A rede corporativa é lógica e fisicamente separada da rede de produção do Azure. O pessoal do Azure acessa a rede corporativa usando estações de trabalho e laptops do Azure. Todos os usuários devem ter uma conta do Azure Active Directory (Azure AD), incluindo um nome de usuário e senha, para acessar recursos de rede corporativa. O acesso à rede corporativa usa contas do Azure AD, que são emitidas para todos os funcionários, prestadores de equipe e fornecedores da Microsoft e gerenciados pela tecnologia de informações da Microsoft. Identificadores de usuário exclusivos diferenciam a equipe com base no status de emprego na Microsoft.

O acesso a aplicativos internos do Azure é controlado por meio da autenticação com o Serviços de Federação do Active Directory (AD FS) (AD FS). AD FS é um serviço hospedado pela tecnologia de informações da Microsoft que fornece autenticação de usuários de rede corporativa por meio da aplicação de um token seguro e declarações de usuário. O AD FS permite que aplicativos internos do Azure autentiquem usuários no domínio do Active Directory corporativo da Microsoft. Para acessar a rede de produção do ambiente de rede corporativa, os usuários devem se autenticar usando a autenticação multifator.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)
