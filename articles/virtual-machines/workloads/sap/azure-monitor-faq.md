---
title: FAQ - Monitor Azure para soluções SAP | Microsoft Docs
description: Neste artigo, aprenda respostas a perguntas frequentes (FAQ) sobre o Azure Monitor para soluções SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377129"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Monitor Azure para soluções SAP FAQ (pré-visualização)
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo fornece respostas a perguntas frequentes (FAQ) sobre o Azure Monitor para soluções SAP.  

 - **Tenho de pagar pelo Azure Monitor para soluções SAP?**  
Não existe qualquer taxa de licenciamento para o Azure Monitor para soluções SAP.  
No entanto, os clientes são responsáveis por suportar o custo dos componentes geridos do grupo de recursos.  

 - **Em que regiões está disponível este serviço para visualização pública?**  
Para pré-visualização pública, este serviço estará disponível em East US 2, West US 2, East US e West Europe.  

 - **Preciso de fornecer permissões para permitir a implantação de um grupo de recursos geridos na minha subscrição?**  
Não, não são necessárias permissões explícitas.  

 - **Onde reside o colecionador VM?**  
No momento da implementação do recurso Azure Monitor for SAP Solutions, recomendamos que os clientes escolham o mesmo VNET para monitorizar o recurso que o seu servidor SAP HANA. Portanto, recomenda-se que o colecionador VM resida no mesmo VNET que o servidor SAP HANA. Se os clientes estiverem a utilizar a base de dados não-HANA, o VM do coletor residirá na mesma base de dados VNET que não-HANA.  

 - **Que versões da HANA são suportadas?**  
HANA 1.0 SPS 12 (Rev. 120 ou superior) e HANA 2.0 SPS03 ou superior  

 - **Quais as configurações de implantação hana suportadas?**  
As configurações a seguir são suportadas:
   - Nó único (escala) e multi-nó (escala)  
   - Recipiente de base de dados único (HANA 1.0 SPS 12) e vários recipientes de base de dados (HANA 1.0 SPS 12 ou HANA 2.0)  
   - Failover do anfitrião automático (n+1) e HSR  

 - **Quais as versões sql server suportadas?**  
SQL Server 2012 SP4 ou superior.  

 - **Quais as configurações do SQL Server suportadas?**  
As configurações a seguir são suportadas:
   - Casos autónomos predefinidos ou nomeados numa máquina virtual  
   - Casos agrupados ou instâncias numa configuração AlwaysOn quando utilizar o nome virtual do recurso agrupado ou o nome do ouvinte AlwaysOn. Atualmente não são recolhidas métricas específicas de cluster ou AlwaysOn    
   - A Azure SQL Database (PAAS) não é atualmente suportada  

 - **O que acontece se eu acidentalmente apagar o grupo de recursos geridos?**  
O grupo de recursos geridos está bloqueado por defeito. Por conseguinte, as hipóteses de supressão acidental do grupo de recursos geridos pelos clientes são minúsculas.  
Se um cliente eliminar o grupo de recursos geridos, o Azure Monitor para soluções SAP deixará de funcionar. O cliente terá de implementar um novo Azure Monitor para recurso SAP Solutions e começar de novo.  

 - **Que papéis preciso na minha assinatura Azure para implementar o Azure Monitor para recurso SAP Solutions?**  
Papel de contribuinte.  

 - **O que é o SLA neste produto?**  
As pré-visualizações estão excluídas dos contratos de nível de serviço. Leia o termo de licença completo através do Azure Monitor para imagem de mercado SAP Solutions.  

 - **Posso monitorizar toda a minha paisagem através desta solução?**  
Atualmente pode monitorizar a base de dados HANA, a infraestrutura subjacente, cluster de alta disponibilidade, servidor Microsoft SQL, disponibilidade de NETWEaver e métricas de disponibilidade de aplicações SAP em pré-visualização pública.  

 - **Este serviço substitui o gestor da SAP Solution?**  
Não. Os clientes ainda podem usar o gestor da SAP Solution para monitorização de processos empresariais.  

 - **Qual é o valor deste serviço sobre soluções tradicionais como SAP HANA Cockpit/Studio?**  
O Azure Monitor for SAP Solutions não é específico da base de dados HANA. O Azure Monitor for SAP Solutions também suporta o AnyDB.  

- **Quais as versões SAP NetWeaver suportadas?**  
SAP NetWeaver 7.0 ou superior.  

- **Quais as configurações DO SAP NetWeaver suportadas?**  
Suporta configurações de servidor de aplicações ABAP, Java e duplo-stack SAP NetWeaver.

- **Por que preciso de desprotegir métodos para a monitorização da aplicação SAP NetWeaver?**  
Nas libertações SAP >= 7.3, a maioria dos métodos de webservice são protegidos por padrão. Para obter métricas de disponibilidade e desempenho, chamando estes métodos, você precisaria de desprotegir os seguintes métodos: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic e GetSystemInstancelist.

- **Existe algum risco em desprotegir os temões SAPCONTROL?**  
Em geral, a desprotegida desprotegidas webmethods SAPCONTROL não representa um risco de segurança como [tal](https://launchpad.support.sap.com/#/notes/1439348), no entanto, se os clientes quiserem restringir/proibir o acesso aos métodos web desprotegidos através das portas do servidor (5XX13 / 5XX14) de sapstartsrv, poderá fazê-lo adicionando filtro na Lista de Controlo de Acesso SAP(ACL), a [nota OSS](https://service.sap.com/sap/support/notes/1495075) descreve a configuração necessária para o conseguir. 

- **Tenho de reiniciar as minhas instâncias SAP depois de ter realizado configurações do sistema para a criação do fornecedor SAP NetWeaver?**  
Sim, uma vez que tenha métodos desprotegidos através de alterações de configuração SAP, terá de reiniciar os respetivos sistemas SAP para garantir que as alterações de configuração são atualizadas.  

## <a name="next-steps"></a>Passos seguintes

- Crie o seu primeiro recurso Azure Monitor para soluções SAP.
