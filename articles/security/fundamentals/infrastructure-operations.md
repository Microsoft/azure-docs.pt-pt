---
title: Gestão da rede de produção Azure - Microsoft Azure
description: Este artigo descreve como a Microsoft gere e opera a rede de produção do Azure para proteger os datacenters do Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68727121"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Gestão e funcionamento da rede de produção azure    
Este artigo descreve como a Microsoft gere e opera a rede de produção do Azure para proteger os datacenters do Azure.

## <a name="monitor-log-and-report"></a>Monitor, log e relatório

A gestão e operação da rede de produção Azure é um esforço coordenado entre as equipas de operações da Azure e da Azure SQL Database. As equipas utilizam várias ferramentas de monitorização do desempenho do sistema e da aplicação no ambiente. E utilizam ferramentas adequadas para monitorizar dispositivos de rede, servidores, serviços e processos de aplicação.

Para garantir a execução segura dos serviços em funcionamento no ambiente Azure, as equipas de operações implementam múltiplos níveis de monitorização, exploração madeireira e reporte, incluindo as seguintes ações:

- Principalmente, o Microsoft Monitoring Agent (MMA) recolhe informações de monitorização e registo de diagnóstico de vários locais, incluindo o controlador de tecidos (FC) e o sistema operativo root (OS), e escreve-os para registar ficheiros. O agente eventualmente empurra um subconjunto digerido da informação para uma conta de armazenamento Azure pré-configurada. Além disso, o serviço de monitorização e diagnóstico independente lê vários dados de monitorização e diagnóstico e resume a informação. O serviço de monitorização e diagnóstico escreve a informação para um registo integrado. O Azure utiliza a monitorização de segurança azure personalizada, que é uma extensão ao sistema de monitorização Azure. Possui componentes que observam, analisam e reportam eventos pertinentes de segurança de vários pontos da plataforma.

- A plataforma Azure SQL Database Windows Fabric fornece serviços de gestão, implementação, desenvolvimento e supervisão operacional para a Base de Dados Azure SQL. A plataforma oferece serviços de implantação distribuídos, multi-passos, monitorização da saúde, reparações automáticas e conformidade com a versão de serviço. Presta os seguintes serviços:

   - Capacidades de modelação de serviços com ambiente de desenvolvimento de alta fidelidade (os clusters datacenter são caros e escassos).
   - Implantação e atualização de um clique para botas de serviço e manutenção.
   - Relatórios de saúde com fluxos de trabalho de reparação automatizados para permitir a auto-cicatrização.
   - Monitorização, alerta e depuração em tempo real através dos nós de um sistema distribuído.
   - Recolha centralizada de dados operacionais e métricas para análise de causas de raiz distribuídas e insight de serviço.
   - Ferramenta operacional para implementação, gestão de alterações e monitorização.
   - A plataforma Azure SQL Database Windows Fabric e scripts de cão de guarda funcionam continuamente e monitorizam em tempo real.

Se ocorrerem anomalias, o processo de resposta ao incidente seguido pela equipa de triagem de incidentes do Azure é ativado. O pessoal de apoio apropriado do Azure é notificado para responder ao incidente. O rastreio e a resolução de problemas são documentados e geridos num sistema centralizado de bilhética. As métricas de tempo de uptime do sistema estão disponíveis ao abrigo do acordo de não-divulgação (NDA) e a pedido.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rede corporativa e acesso multifactor à produção
A base de utilizadores da rede corporativa inclui pessoal de suporte Azure. A rede corporativa suporta funções corporativas internas e inclui acesso a aplicações internas que são usadas para o apoio ao cliente azure. A rede corporativa está separada logicamente e fisicamente da rede de produção azure. O pessoal azure acede à rede corporativa utilizando postos de trabalho Azure e portáteis. Todos os utilizadores devem ter uma conta Azure Ative Directory (Azure AD), incluindo um nome de utilizador e senha, para aceder aos recursos da rede corporativa. O acesso à rede corporativa utiliza contas AD Azure, que são emitidas a todo o pessoal da Microsoft, empreiteiros e fornecedores e geridas pela Microsoft Information Technology. Os identificadores de utilizador únicos distinguem o pessoal com base no seu estado de emprego na Microsoft.

O acesso a aplicações internas do Azure é controlado através da autenticação com os Serviços da Federação de Diretórios Ativos (AD FS). O AD FS é um serviço hospedado pela Microsoft Information Technology que fornece a autenticação dos utilizadores da rede corporativa através da aplicação de um token seguro e de reclamações de utilizadores. O AD FS permite que as aplicações internas do Azure autentiem os utilizadores contra o domínio de diretório ativo corporativo da Microsoft. Para aceder à rede de produção a partir do ambiente da rede corporativa, os utilizadores devem autenticar utilizando a autenticação de vários fatores.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Rede de produção azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)
