---
title: Gestão de rede de produção do Azure - Microsoft Azure
description: Este artigo descreve como a Microsoft gere e opera a rede de produção do Azure para proteger dos datacenters do Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: 515896851c5bf7b9c847416ebadedf1242e0eeb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428260"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Gestão e funcionamento da rede de produção do Azure    
Este artigo descreve como a Microsoft gere e opera a rede de produção do Azure para proteger dos datacenters do Azure.

## <a name="monitor-log-and-report"></a>Monitor, log e relatório

A gestão e o funcionamento da rede de produção do Azure é um esforço coordenado entre as equipes de operações do Azure e base de dados do Azure SQL. O uso de equipes, ferramentas de várias sistema e monitorização de desempenho da aplicação no ambiente. E utilizam as ferramentas apropriadas para monitorizar dispositivos de rede, servidores, serviços e processos da aplicação.

Para garantir a execução segura de serviços em execução no ambiente Azure, as equipes de operações, implemente vários níveis de monitorização, registo e relatórios, incluindo as seguintes ações:

- Principalmente, o Microsoft Monitoring Agent (MMA) recolhe informações de registo de monitorização e diagnóstico de muitos lugares, incluindo o controlador de malha (FC) e o sistema de operativo raiz (SO) e escreve-o para ficheiros de registo. O agente envia, por fim, um subconjunto digested das informações para uma conta de armazenamento do Azure pré-configurada. Além disso, o monitoramento freestanding e o serviço de diagnóstico lê vários dados de monitorização e diagnóstico de registo e resume as informações. O serviço de monitorização e diagnóstico escreve as informações num registo integrado. O Azure utiliza a segurança do Azure personalizada de monitorização, que é uma extensão para o Azure, sistema de monitoramento. Ele possui componentes que observam, analisar e criar relatem sobre eventos pertinentes para a segurança de vários pontos na plataforma.

- A plataforma de recursos de infraestrutura do Windows do Azure SQL da base de dados fornece serviços de supervisão operacional para a base de dados do Azure SQL, desenvolvimento, implementação e gestão. A plataforma oferece serviços de implementação distribuída, com vários passos, a monitorização de estado de funcionamento, reparações automáticas e conformidade de versão do serviço. Fornece os seguintes serviços:

   - Serviço de recursos com o ambiente de desenvolvimento de alta fidelidade de modelagem (clusters do Centro de dados são escassos e Caro).
   - Implementação de um só clique e fluxos de trabalho de atualização para o arranque de serviço e manutenção.
   - Estado de funcionamento de relatórios com fluxos de trabalho de reparação automática para ativar a autorrecuperação.
   - Monitorização em tempo real, alertas e recursos de depuração em todos os nós de um sistema distribuído.
   - Coleção centralizada de dados operacionais e de métricas para raiz distribuída fazer com que as informações de análise e serviço.
   - Operacional de ferramentas para a implementação, monitorização e gestão de alterações.
   - Os scripts de plataforma e watchdog de recursos de infraestrutura do Windows do Azure SQL da base de dados continuamente a executar e monitorizar em tempo real.

Se ocorrerem quaisquer anomalias, o processo de resposta a incidentes, seguido pela equipe de triagem de incidentes do Azure é ativado. O pessoal de suporte do Azure adequada notificado para responder ao incidente. Controlo de problemas e resolução são documentados e gerenciados num sistema de pedidos de suporte centralizado. As métricas de tempo de atividade do sistema estão disponíveis sob o contrato de confidencialidade (NDA) e a pedido.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rede empresarial e de acesso multifator para produção
A base de utilizadores de rede empresarial inclui pessoal de suporte do Azure. A rede empresarial suporta funções empresariais internas e inclui acesso a aplicativos internos que são utilizados para suporte ao cliente do Azure. A rede corporativa é logicamente e fisicamente separada da rede de produção do Azure. O pessoal do Azure aceder à rede empresarial através da utilização de estações de trabalho do Azure e laptops. Todos os utilizadores têm de ter uma conta do Azure Active Directory (Azure AD), incluindo um nome de utilizador e palavra-passe para aceder a recursos de rede empresarial. Acesso à rede empresarial utiliza as contas do Azure AD, que são emitidas para todas as equipas da Microsoft, contratados e fornecedores e geridas pela tecnologia da informação da Microsoft. Identificadores de utilizador exclusivo distinguem equipe com base no respetivo estado de emprego na Microsoft.

Acesso a aplicações do Azure internos é controlado através da autenticação com os serviços de Federação do Active Directory (AD FS). AD FS é um serviço hospedado pela tecnologia de informações da Microsoft que fornece autenticação de utilizadores de rede empresarial através de aplicar um afirmações de utilizador e de token seguro. AD FS permite que as aplicações do Azure internas autenticar os usuários contra o domínio de empresa do Active Directory do Microsoft. Para acessar a rede de produção do ambiente de rede empresarial, os utilizadores tem de ser autenticado utilizando a autenticação multifator.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)
