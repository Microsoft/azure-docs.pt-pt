---
title: Diretório Ativo Azure reporta FAQ Microsoft Docs
description: Perguntas frequentes em torno dos relatórios do Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668486"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Perguntas frequentes em torno de relatórios do Diretório Ativo do Azure

Este artigo inclui respostas a perguntas frequentes sobre relatórios de Diretório Ativo Azure (Azure AD). Para obter mais informações, veja [Relatórios do Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introdução 

**P: Atualmente uso as APIs `https://graph.windows.net/<tenant-name>/reports/` ponto final para puxar a auditoria da AD Azure e relatórios integrados de utilização de aplicações nos nossos sistemas de reporte programáticamente. Para onde devo mudar?**

**A:** Procure a referência da [API](https://developer.microsoft.com/graph/) para ver como pode [utilizar as APIs para aceder a relatórios](concept-reporting-api.md)de atividade . Este ponto final tem dois relatórios (**Auditoria** e **Sign-ins)** que fornecem todos os dados que obteve no antigo ponto final da API. Este novo ponto final também tem um relatório de entrada com a licença Azure AD Premium que pode usar para obter o uso da app, utilização do dispositivo e informações de entrada do utilizador.

---

**P: Atualmente uso as APIs de ponto final `https://graph.windows.net/<tenant-name>/reports/` para puxar os relatórios de segurança da AD Azure (tipos específicos de deteções, tais como credenciais vazadas ou inscrições de endereços IP anónimos) para os nossos sistemas de reporte programáticamente. Para onde devo mudar?**

**A:** Pode utilizar as deteções de risco de [Proteção de Identidade API](../identity-protection/graph-get-started.md) para aceder a deteções de segurança através do Microsoft Graph. Este novo formato confere maior flexibilidade na forma como pode consultar dados, com filtragem avançada, seleção de campo e muito mais, e normaliza as deteções de risco num só tipo para uma integração mais fácil em SIEMs e outras ferramentas de recolha de dados. Como os dados estão num formato diferente, não pode substituir uma nova consulta pelas suas antigas consultas. No entanto, [o novo API utiliza o Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que é o padrão da Microsoft para APIs como O365 ou Azure AD. Assim, o trabalho necessário pode estender os seus investimentos atuais do Microsoft Graph ou ajudá-lo a iniciar a sua transição para esta nova plataforma padrão.

---

**P: Como posso obter uma licença premium?**

**A:** Ver [Começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição de Diretório Ativo Azure.

---

**P: Em quanto tempo devo ver os dados das atividades depois de obter uma licença premium?**

**A:** Se já tem dados de atividades como licença gratuita, então pode vê-lo imediatamente. Se não tiver dados, levará um ou dois dias para os dados aparecerem nos relatórios.

---

**P: Posso ver os dados do mês passado depois de obter uma licença de prémio Azure AD?**

**A:** Se mudou recentemente para uma versão Premium (incluindo uma versão experimental), pode ver dados até 7 dias inicialmente. Quando os dados se acumulam, pode ver dados nos últimos 30 dias.

---

**P: Preciso de ser um administrador global para ver os sign-ins de atividade no portal Azure ou para obter dados através da API?**

**A:** Não, também pode aceder aos dados de reporte através do portal ou através da API se for um Leitor de **Segurança** ou Administrador de **Segurança** para o inquilino. É claro que os **Administradores Globais** também terão acesso a estes dados.

---


## <a name="activity-logs"></a>Registos de atividade


**P: Qual é a retenção de dados para registos de atividade (Auditoria e Sign-ins) no portal Azure?** 

**A:** A tabela seguinte enumera o período de retenção de dados para os registos de atividade. Para obter mais informações, consulte as políticas de retenção de [dados para relatórios da AD Azure](reference-reports-data-retention.md).

| Relatório                 | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Registos de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Inícios de sessão               | N/D           | 30 dias             | 30 dias             |
| Utilização azure MFA        | 30 dias       | 30 dias             | 30 dias             |

---

**P: Quanto tempo demora até ver os dados da atividade depois de ter completado a minha tarefa?**

**A:** Os registos de auditoria têm uma latência que varia entre 15 minutos e uma hora. Os registos de atividade de iniciação podem demorar de 15 minutos a até 2 horas para alguns registos.

---

**P: Posso obter informações de registo de atividade sessão do Office 365 através do portal Azure?**

**A:** Mesmo que a atividade do Office 365 e os registos de atividade do Azure AD partilhem muitos dos recursos de diretório, se quiser uma visão completa dos registos de atividade do Office 365, deve ir ao centro de administração da [Microsoft 365](https://admin.microsoft.com) para obter informações de registo de atividade seletiva do Office 365.

---

**P: Que APIs uso para obter informações sobre registos de atividade seletivas do Office 365?**

**A:** Utilize o [Office 365 Management APIs](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) para aceder aos registos de atividade do Office 365 através de uma API.

---

**P: Quantos discos posso descarregar do portal Azure?**

**A:** Você pode baixar até 5000 registos do portal Azure. Os registos são classificados pela *mais recente* e por padrão, obtém-se os mais recentes 5000 registos.

---

## <a name="risky-sign-ins"></a>Inícios de sessão de risco

**P: Existe uma deteção de risco na Proteção de Identidade, mas não vejo o registo correspondente no relatório de inscrição. Isto é esperado?**

**A:** Sim, a Proteção de Identidade avalia o risco para todos os fluxos de autenticação, interativos ou não interativos. No entanto, todos os relatórios de inscrição mostram apenas os sign-ins interativos.

---

**P: Como sei por que razão um sessão ou um utilizador foi sinalizado como arriscado no portal Azure?**

**A:** Se tiver uma subscrição **Azure AD Premium,** poderá saber mais sobre as deteções de risco subjacentes selecionando o utilizador em **Utilizadores sinalizados para riscos** ou selecionando um registo no relatório de entrada de **Risco.** Se tiver uma subscrição **Gratuita** ou **Básica,** então pode ver os utilizadores em risco e relatórios de entrada de risco, mas não consegue ver as informações subjacentes à deteção de riscos.

---

**P: Como os endereços IP são calculados nos relatórios de inscrição e de inscrição de risco?**

**A:** Os endereços IP são emitidos de modo a que não exista uma ligação definitiva entre um endereço IP e onde o computador com esse endereço está fisicamente localizado. O mapeamento de endereços IP é ainda mais complicado por fatores como fornecedores móveis e VPNs que emitem endereços IP de piscinas centrais muitas vezes muito longe de onde o dispositivo cliente é realmente usado. Atualmente nos relatórios da AD Azure, converter o endereço IP para uma localização física é um esforço melhor baseado em vestígios, dados de registo, retrospetivas inversas e outras informações. 

---

**P: O que significa a deteção de risco "Iniciar sessão com risco adicional detetado" significa?**

**A:** Para lhe dar uma ideia de todos os sign-ins de risco no seu ambiente, funções de "iniciar sessão com risco adicional detetado" como espaço reservado para inscrições para deteções exclusivas dos assinantes da Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Acesso Condicional

**P: Quais as novidades com esta funcionalidade?**

**A:** Os clientes podem agora resolver as políticas de Acesso Condicional através de todos os relatórios de inscrição. Os clientes podem rever o estado de Acesso Condicional e mergulhar nos detalhes das políticas que se aplicavam ao sign-in e ao resultado de cada apólice.

**P: Como posso começar?**

**A:** Para começar:

* Navegue para o relatório de inscrição no [portal Azure.](https://portal.azure.com)
* Clique no sessão que pretende resolver.
* Navegue para o separador **Acesso Condicional.** Aqui, pode ver todas as políticas que impactaram o sign-in e o resultado de cada política. 
    
**P: Quais são todos os valores possíveis para o estado de Acesso Condicional?**

**A:** O estado de Acesso Condicional pode ter os seguintes valores:

* **Não Aplicada**: Isto significa que não existiu nenhuma política de CA com o utilizador e app no âmbito. 
* **Sucesso**: Isto significa que havia uma política de AC com o utilizador e app no âmbito e as políticas de CA foram satisfeitas com sucesso. 
* **Falha**: Isto significa que havia uma política de AC com o utilizador e app no âmbito e as políticas de CA não estavam satisfeitas. 
    
**P: Quais são todos os valores possíveis para o resultado da política de Acesso Condicional?**

**A:** Uma política de acesso condicional pode ter os seguintes resultados:

* **Sucesso**: A política foi satisfeita com sucesso.
* **Fracasso**: A política não foi satisfeita.
* **Não aplicado**: Isto pode acontecer porque as condições políticas não se reuniam.
* **Não ativado**: Isto deve-se à política em estado de deficiência. 
    
**P: O nome da política no relatório de todos os relatórios de inscrição não corresponde ao nome da política em CA. Porquê?**

**A:** O nome da política no relatório de todos os relatórios de inscrição baseia-se no nome da política da AC no momento da inscrição. Isto pode ser inconsistente com o nome da política na AC se atualizar o nome da apólice mais tarde, isto é, após o início do início.

**P: A minha inscrição foi bloqueada devido a uma política de acesso condicional, mas o relatório de atividade de inscrição mostra que o sinal de inscrição foi bem sucedido. Porquê?**

**A:** Atualmente, o relatório de insessão pode não apresentar resultados precisos para os cenários exchange ActiveSync quando o Acesso Condicional é aplicado. Pode haver casos em que o resultado do relatório mostra um registo bem sucedido, mas o registo falhou devido a uma política de acesso condicional. 
