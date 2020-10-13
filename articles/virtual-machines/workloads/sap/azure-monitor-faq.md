---
title: FAQ - Monitor Azure para Soluções SAP Microsoft Docs
description: Neste artigo, aprenda respostas a perguntas frequentes (FAQ) sobre o monitor Azure para soluções SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: f584601c2dcbea989f1b68d9fbac5a7f53a59d86
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994230"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Monitor Azure para soluções SAP FAQ (pré-visualização)
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo fornece respostas a perguntas frequentes (FAQ) sobre o monitor Azure para soluções SAP.  

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
Atualmente pode monitorizar a base de dados HANA, a infraestrutura subjacente, o cluster de alta disponibilidade e o servidor Microsoft SQL na pré-visualização pública.  

 - **Este serviço substitui o gestor da SAP Solution?**  
Não. Os clientes ainda podem usar o gestor da SAP Solution para monitorização de processos empresariais.  

 - **Qual é o valor deste serviço sobre soluções tradicionais como SAP HANA Cockpit/Studio?**  
O Azure Monitor for SAP Solutions não é específico da base de dados HANA. O Azure Monitor for SAP Solutions também suporta o AnyDB.  

## <a name="next-steps"></a>Passos seguintes

- Crie o seu primeiro recurso Azure Monitor para soluções SAP.
