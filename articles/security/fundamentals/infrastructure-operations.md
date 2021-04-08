---
title: Gestão da rede de produção Azure - Microsoft Azure
description: Este artigo descreve como a Microsoft gere e opera a rede de produção Azure para proteger os datacenters do Azure.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727121"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Gestão e funcionamento da rede de produção da Azure    
Este artigo descreve como a Microsoft gere e opera a rede de produção Azure para proteger os datacenters do Azure.

## <a name="monitor-log-and-report"></a>Monitor, log e relatório

A gestão e funcionamento da rede de produção Azure é um esforço coordenado entre as equipas de operações da Azure e da Azure SQL Database. As equipas utilizam várias ferramentas de monitorização do desempenho do sistema e aplicação no ambiente. E usam ferramentas apropriadas para monitorizar dispositivos de rede, servidores, serviços e processos de aplicação.

Para garantir a execução segura dos serviços em execução no ambiente Azure, as equipas de operações implementam múltiplos níveis de monitorização, registo e reporte, incluindo as seguintes ações:

- Em primeiro lugar, o Microsoft Monitoring Agent (MMA) recolhe informações de monitorização e de registo de diagnóstico de muitos locais, incluindo o controlador de tecido (FC) e o sistema operativo raiz (OS), e escreve-o para registar ficheiros. O agente eventualmente empurra um subconjunto digerido da informação para uma conta de armazenamento Azure pré-configurada. Além disso, o serviço de monitorização e diagnóstico independente lê vários dados de registo de monitorização e diagnóstico e resume a informação. O serviço de monitorização e diagnóstico escreve a informação para um registo integrado. O Azure utiliza a monitorização de segurança Azure, construída sob medida, que é uma extensão ao sistema de monitorização Azure. Tem componentes que observam, analisam e reportam eventos pertinentes de segurança de vários pontos da plataforma.

- A plataforma Azure SQL Database Windows Fabric fornece serviços de gestão, implementação, desenvolvimento e supervisão operacional para a Base de Dados Azure SQL. A plataforma oferece serviços de implantação distribuídos, multi-passo, monitorização de saúde, reparações automáticas e conformidade da versão de serviço. Presta os seguintes serviços:

   - Capacidades de modelação de serviços com ambiente de desenvolvimento de alta fidelidade (os clusters do datacenter são caros e escassos).
   - Implementação de um clique e atualização de fluxos de trabalho para a instalação de botas de serviço e manutenção.
   - Relatórios de saúde com fluxos de trabalho de reparação automatizados para permitir a auto-cicatrização.
   - Monitorização em tempo real, alerta e depuração de instalações através dos nós de um sistema distribuído.
   - Recolha centralizada de dados operacionais e métricas para análise de causa de raiz distribuída e visão de serviço.
   - Ferramenta operacional para implantação, gestão de alterações e monitorização.
   - A plataforma Azure SQL Database Windows Fabric e scripts watchdog funcionam continuamente e monitorizam em tempo real.

Se ocorrerem anomalias, o processo de resposta a incidentes seguido pela equipa de triagem de incidentes do Azure é ativado. O pessoal de apoio adequado da Azure é notificado para responder ao incidente. O rastreio e a resolução de problemas são documentados e geridos num sistema centralizado de bilhética. As métricas de tempo de uptime do sistema estão disponíveis ao abrigo do acordo de não divulgação (NDA) e a pedido.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rede corporativa e acesso a vários fatores à produção
A base de utilizadores da rede corporativa inclui pessoal de suporte Azure. A rede corporativa suporta funções corporativas internas e inclui acesso a aplicações internas que são usadas para o suporte ao cliente da Azure. A rede corporativa está separada logicamente e fisicamente da rede de produção Azure. O pessoal da Azure acede à rede corporativa utilizando estações de trabalho e portáteis da Azure. Todos os utilizadores devem ter uma conta Azure Ative Directory (Azure AD), incluindo um nome de utilizador e senha, para aceder aos recursos da rede corporativa. O acesso à rede corporativa utiliza contas AZure AD, que são emitidas a todos os funcionários da Microsoft, empreiteiros e fornecedores e geridas pela Microsoft Information Technology. Os identificadores de utilizadores únicos distinguem o pessoal com base no seu estatuto de emprego na Microsoft.

O acesso às aplicações internas do Azure é controlado através da autenticação com serviços da Federação de Diretórios Ativos (AD FS). O AD FS é um serviço hospedado pela Microsoft Information Technology que fornece a autenticação dos utilizadores de redes corporativas através da aplicação de um token seguro e reivindicações de utilizadores. O AD FS permite que as aplicações internas do Azure autuçam os utilizadores contra o domínio do diretório ativo da Microsoft. Para aceder à rede de produção a partir do ambiente de rede corporativa, os utilizadores devem autenticar utilizando a autenticação de vários fatores.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
