---
title: Azure Ative Directory Reportes FAQ | Microsoft Docs
description: Perguntas frequentes em torno de relatórios do Azure Ative Directory.
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
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b6b182d030c267dcec7ed57c0c0dd1901b0cb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935092"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Perguntas frequentes em torno de relatórios do Azure Ative Directory

Este artigo inclui respostas a perguntas frequentes sobre relatórios do Azure Ative Directory (Azure AD). Para obter mais informações, veja [Relatórios do Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introdução 

**P: Atualmente uso as APIs de `https://graph.windows.net/<tenant-name>/reports/` ponto final para puxar a auditoria AZure AD e relatórios integrados de utilização de aplicações nos nossos sistemas de reporte programáticamente. Para que devo mudar?**

**A:** Consulte a referência da [API](https://developer.microsoft.com/graph/) para ver como pode [utilizar as APIs para aceder a relatórios de atividades.](concept-reporting-api.md) Este ponto final tem dois relatórios **(Auditoria** e **Inscrições)** que fornecem todos os dados que obteve no antigo ponto final da API. Este novo ponto final também tem um relatório de ins-ins com a licença Azure AD Premium que você pode usar para obter a utilização de aplicações, uso do dispositivo e informações de início de súm.

---

**P: Atualmente, uso as `https://graph.windows.net/<tenant-name>/reports/` APIs de ponta para extrair relatórios de segurança AZure AD (tipos específicos de deteções, tais como credenciais vazadas ou entradas de endereços IP anónimos) nos nossos sistemas de reporte programáticamente. Para que devo mudar?**

**A:** Pode utilizar a [API de deteção de risco de Proteção de Identidade](../identity-protection/howto-identity-protection-graph-api.md) para aceder a deteções de segurança através do Microsoft Graph. Este novo formato proporciona uma maior flexibilidade na forma como se pode consultar dados, com filtragem avançada, seleção de campo e muito mais, e normaliza as deteções de risco num único tipo para uma integração mais fácil em SIEMs e outras ferramentas de recolha de dados. Como os dados estão num formato diferente, não pode substituir uma nova consulta para as suas consultas antigas. No entanto, [a nova API utiliza o Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta), que é o padrão da Microsoft para APIs como o Microsoft 365 ou AZure AD. Assim, o trabalho necessário pode estender os seus investimentos atuais do Microsoft Graph ou ajudá-lo a iniciar a sua transição para esta nova plataforma padrão.

---

**P: Como consigo uma licença premium?**

**A:** Veja [como começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Ative Directory.

---

**P: Quando devo ver os dados das atividades depois de obter uma licença premium?**

**A:** Se já tem dados de atividades como licença gratuita, então pode vê-lo imediatamente. Se não tiver quaisquer dados, levará até 3 dias para que os dados apareçam nos relatórios.

---

**P: Posso ver os dados do mês passado depois de obter uma licença premium AZure AD?**

**A:** Se mudou recentemente para uma versão Premium (incluindo uma versão experimental), pode ver dados até 7 dias inicialmente. Quando os dados se acumulam, pode ver dados dos últimos 30 dias.

---

**P: Preciso de ser um administrador global para ver os acessos de atividade ao portal Azure ou para obter dados através da API?**

**A:** Não, também pode aceder aos dados de reporte através do portal ou através da API se for um Leitor de **Segurança** ou **Administrador de Segurança** para o arrendatário. É claro que **os Administradores Globais** também terão acesso a estes dados.

---


## <a name="activity-logs"></a>Registos de atividade


**P: Qual é a retenção de dados para registos de atividade (Auditoria e Logins) no portal Azure?** 

**A:** Para obter mais informações, consulte [as políticas de retenção de dados para relatórios Azure AD](reference-reports-data-retention.md).

---

**P: Quanto tempo demora até poder ver os dados da atividade depois de ter completado a minha tarefa?**

**A:** Os registos de auditoria têm uma latência que varia entre 15 minutos e uma hora. Os registos de atividade de login podem demorar de 15 minutos a até 2 horas para alguns registos.

---

**P: Posso obter informações de registo de atividades do Microsoft 365 através do portal Azure?**

**A:** Mesmo que a atividade da Microsoft 365 e os registos de atividade azure AD partilhem muitos dos recursos do diretório, se quiser uma visão completa dos registos de atividades da Microsoft 365, deverá ir ao [centro de administração microsoft 365](https://admin.microsoft.com) para obter informações de registo de atividade do Office 365.

---

**P: Quais APIs eu uso para obter informações sobre os registos de atividade do Microsoft 365?**

**A:** Utilize as [APIs de Gestão Microsoft 365](/office/office-365-management-api/office-365-management-apis-overview) para aceder aos registos de atividade do Microsoft 365 através de uma API.

---

**P: Quantos registos posso descarregar do portal Azure?**

**A:** Você pode baixar até 5000 registos a partir do portal Azure. Os registos são classificados pela *mais recente* e por defeito, obtém-se os registos mais recentes.

---

## <a name="risky-sign-ins"></a>Inícios de sessão de risco

**P: Há uma deteção de risco na Proteção de Identidade, mas não estou a ver o correspondente sinal no relatório de inscrições. Isto é esperado?**

**A:** Sim, a Proteção de Identidade avalia o risco para todos os fluxos de autenticação, sejam interativos ou não interativos. No entanto, todos os relatórios de inscrição mostram apenas os relatórios interativos.

---

**P: Como sei por que um sinal ou um utilizador foi sinalizado como arriscado no portal Azure?**

**A:** Se tiver uma subscrição **Azure AD Premium,** pode saber mais sobre as deteções de risco subjacentes selecionando o utilizador em **Utilizadores sinalizados para risco** ou selecionando um registo no relatório **de insus máximos de risco.** Se tiver uma subscrição **Gratuita** ou **Básica,** poderá visualizar os utilizadores em risco e relatórios de ins-ins de risco, mas não pode ver as informações subjacentes à deteção de riscos.

---

**P: Como são calculados os endereços IP no relatório de ins e inscrições arriscadas?**

**A:** Os endereços IP são emitidos de forma a que não exista uma ligação definitiva entre um endereço IP e onde o computador com esse endereço esteja fisicamente localizado. O mapeamento de endereços IP é ainda mais complicado por fatores como fornecedores móveis e VPNs que emitem endereços IP de piscinas centrais muitas vezes muito longe de onde o dispositivo cliente é realmente usado. Atualmente nos relatórios AD AZure, converter o endereço IP para uma localização física é um melhor esforço com base em vestígios, dados de registo, análises inversas e outras informações. 

---

**P: O que significa a deteção de risco "Iniciar sção com risco adicional detetado"?**

**A:** Para lhe dar uma visão de todos os insus máximos de entrada no seu ambiente, as funções "Iniciar súpido com risco adicional detetado" funcionam como espaço reservado para acessos para deteções exclusivas dos assinantes da Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Acesso Condicional

**P: Quais as novidades com esta funcionalidade?**

**A:** Os clientes podem agora resolver as políticas de acesso condicional através de todos os relatórios de acesso. Os clientes podem rever o estado de Acesso Condicional e mergulhar nos detalhes das políticas que se aplicavam ao sign-in e o resultado de cada política.

**P: Como é que começo?**

**A:** Para começar:

* Navegue para o relatório de inscrições no [portal Azure](https://portal.azure.com).
* Clique no sinal de que deseja resolver problemas.
* Navegue para o separador **acesso condicional.** Aqui, pode ver todas as políticas que impactaram o sign-in e o resultado de cada política. 
    
**P: Quais são todos os valores possíveis para o estado de Acesso Condicional?**

**A:** O estado de acesso condicional pode ter os seguintes valores:

* **Não Aplicado**: Isto significa que não havia nenhuma política de Acesso Condicional com o utilizador e app no âmbito. 
* **Sucesso**: Isto significa que havia uma política de acesso condicional com o utilizador e app no âmbito e as políticas de Acesso Condicional foram satisfeitas com sucesso. 
* **Falha**: A inscrição satisfez o estado de utilização e aplicação de pelo menos uma política de acesso condicional e os controlos de concessão não estão satisfeitos ou definidos para bloquear o acesso.
    
**P: Quais são todos os valores possíveis para o resultado da política de acesso condicional?**

**A:** Uma política de acesso condicional pode ter os seguintes resultados:

* **Sucesso**: A política foi satisfeita com sucesso.
* **Falha:** A política não foi satisfeita.
* **Não aplicado**: Isto pode ser porque as condições políticas não cumpriram.
* **Não ativado**: Isto deve-se à política em estado de deficientes. 
    
**P: O nome da política no relatório de todos os relatórios de inscrição não corresponde ao nome da política em CA. Porquê?**

**A:** O nome da política no relatório de inscrição baseia-se no nome da política de acesso condicional no momento da inscrição. Isto pode ser inconsistente com o nome da política na AC se atualizar o nome da apólice mais tarde, isto é, após a inscrição.

**P: A minha inscrição foi bloqueada devido a uma política de acesso condicional, mas o relatório de atividades de inscrição mostra que o sinal foi bem sucedido. Porquê?**

**A:** Atualmente, o relatório de inscrição pode não apresentar resultados precisos para os cenários Do Exchange ActiveSync quando o Acesso Condicional é aplicado. Pode haver casos em que o resultado do sindudo no relatório mostra uma s indicação bem sucedida, mas o sin-in falhou devido a uma política de acesso condicional.
