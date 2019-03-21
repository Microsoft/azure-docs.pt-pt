---
title: FAQ de relatórios do Azure Active Directory | Documentos da Microsoft
description: Perguntas mais frequentes quesitons em torno de relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 274675c3b9f04877f5665efbcbf7951a5bbb0e27
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57833185"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Perguntas mais frequentes sobre em torno de relatórios do Azure Active Directory

Este artigo contém respostas para perguntas mais frequentes sobre o Azure Active Directory (Azure AD) de relatórios. Para obter mais informações, veja [Relatórios do Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introdução 

**P: Posso utilizar atualmente o `https://graph.windows.net/<tenant-name>/reports/` ponto final de APIs para auditoria de solicitação do Azure AD e a utilização de aplicação integrada relatórios em nossos sistemas de relatórios por meio de programação. O que posso alternar para?**

**R:** Procurar os [referência da API](https://developer.microsoft.com/graph/) para ver como é possível [usar as APIs para aceder aos relatórios de atividade](concept-reporting-api.md). Este ponto de extremidade tem dois relatórios (**auditoria** e **inícios de sessão**) que fornecem a todos os dados de obteve o antigo ponto de final de API. Este novo ponto de extremidade também tem um relatório de inícios de sessão com a licença do Azure AD Premium que pode utilizar para obter a utilização da aplicação, a utilização de dispositivos e informações de início de sessão de utilizador.

---

**P: Posso utilizar atualmente o `https://graph.windows.net/<tenant-name>/reports/` ponto final de APIs para enviar relatórios de segurança do Azure AD (tipos específicos de detecções, tais como a fuga de credenciais ou inícios de sessão de endereços IP anónimos) em nossos sistemas de relatórios por meio de programação. O que posso alternar para?**

**R:** Pode utilizar o [eventos de risco do Identity Protection API](../identity-protection/graph-get-started.md) para deteções de segurança de acesso através do Microsoft Graph. Esse novo formato proporciona maior flexibilidade na forma como pode consultar os dados, com filtragem avançada, seleção de campo e muito mais e padroniza eventos de risco num tipo de integração mais fácil com SIEMs e outras ferramentas de recolha de dados. Uma vez que os dados estão num formato diferente, não é possível substituir uma nova consulta para as suas consultas antigas. No entanto, [a nova API utiliza o Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que é o padrão da Microsoft para essas APIs, como o Office 365 ou do Azure AD. Para que o trabalho necessário a estender seus investimentos atuais do MS Graph ou ajuda a começar sua transição para essa nova plataforma padrão.

---

**P: Como posso obter uma licença premium?**

**R:** Ver [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Active Directory.

---

**P: Quando posso ver os dados de atividades depois de obter uma licença premium?**

**R:** Se já tiver dados de atividades como uma licença gratuita, em seguida, pode vê-lo imediatamente. Se não tiver quaisquer dados, em seguida, irá demorar um ou dois dias para os dados sejam apresentados nos relatórios.

---

**P: Pode ver dados do mês anterior depois de obter uma licença do Azure AD premium?**

**R:** Se recentemente tiver mudado para uma versão Premium (incluindo uma versão de avaliação), pode ver dados de cópia de segurança a 7 dias inicialmente. Quando os dados acumulam-se, pode ver os dados nos últimos 30 dias.

---

**P: É necessário ser um administrador global para ver os atividade inícios de sessão no portal do Azure ou para obter dados através da API?**

**R:** Não, também pode acessar os dados de relatório através do portal ou através da API se for um **leitor de segurança** ou **administrador de segurança** para o inquilino. É claro **os administradores globais** também terá acesso a estes dados.

---


## <a name="activity-logs"></a>Registos de atividade


**P: O que é o período de retenção de dados de registos de Atividades (auditorias e inícios de sessão) no portal do Azure?** 

**R:** A tabela seguinte lista o período de retenção de dados para os registos de atividade. Para obter mais informações, consulte [políticas de retenção de dados para relatórios do Azure AD](reference-reports-data-retention.md).

| Relatório                 | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Registos de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Inícios de sessão               | N/A           | 30 dias             | 30 dias             |
| Utilização MFA do Azure        | 30 dias       | 30 dias             | 30 dias             |

---

**P: Quanto tempo demora até que eu possa ver os dados de atividade depois de eu ter concluído a minha tarefa?**

**R:** Registos de auditoria tem uma latência desde de 15 minutos até uma hora. Registos de atividades de início de sessão podem demorar entre 15 minutos até 2 horas para alguns registos.

---

**P: Pode obter informações de registo de atividade do Office 365 através do portal do Azure?**

**R:** Apesar de atividades do Office 365 e a partilha de registos de atividade do Azure AD muitos dos recursos de diretório, se desejar uma visão completa dos registos de atividades do Office 365, deve ir para o [Centro de administração do Microsoft 365](https://admin.microsoft.com) para obter o registo de atividades do Office 365 informações.

---

**P: Os APIs de uso para obter informações sobre os registos de atividades do Office 365?**

**R:** Utilize o [APIs de gestão do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) para aceder os registos de atividades do Office 365 através de uma API.

---

**P: O número de registos, pode transferir do portal do Azure?**

**R:** Pode baixar até 5000 registos do portal do Azure. Os registos são ordenados pelo *mais recente* e por padrão, obtém os registos de 5000 mais recentes.

---

## <a name="risky-sign-ins"></a>Inícios de sessão de risco

**P: Existe um evento de risco no Identity Protection, mas não estou vendo correspondente início de sessão no relatório de inícios de sessão. É isto esperado?**

**R:** Sim, Identity Protection avalia o risco para todos os fluxos de autenticação se interativos ou não-interativa. No entanto, todos os relatórios de apenas inícios de sessão mostra apenas os interativos inícios de sessão.

---

**P: Como posso saber por que um início de sessão ou um utilizador foi sinalizado risco no portal do Azure?**

**R:** Se tiver uma **do Azure AD Premium** subscrição, pode saber mais sobre os eventos de risco subjacentes ao selecionar o utilizador na **utilizadores sinalizados para risco** ou ao selecionar um registo no **duvidosos inícios de sessão** relatório. Se tiver um **gratuito** ou **básica** subscrição, em seguida, pode ver os utilizadores em risco e relatórios de inícios de sessão de risco, mas não é possível ver as informações de eventos de risco subjacentes.

---

**P: Como são calculados os endereços IP nos inícios de sessão e relatório de inícios de sessão de risco?**

**R:** Endereços IP são emitidos de forma que não existe nenhuma ligação definitiva entre um endereço IP e em que o computador com esse endereço está fisicamente localizado. Mapear endereços IP é ainda mais complicado por fatores como fornecedores de móveis e VPNs de emissão de endereços IP de pools centrais, muitas vezes, muito longe de ser em que o dispositivo de cliente, na verdade, é utilizado. Atualmente, nos relatórios do Azure AD, a conversão de endereço IP para uma localização física é um melhor esforço com base em rastreamentos, os dados de registo, pesquisas inversa e outras informações. 

---

**P: O que faz o evento de risco "Início de sessão com risco adicional detetado" significar?**

**R:** Para lhe fornecer informações sobre todos os inícios de sessão arriscados no seu ambiente, as funções de "Início de sessão com risco adicional detetado" como espaço reservado para inícios de sessão de deteções que são exclusivas para subscritores do Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Acesso condicional

**P: O que há de novo com esta funcionalidade?**

**R:** Os clientes agora podem resolver problemas de políticas de acesso condicional através de todos os relatórios de inícios de sessão. Os clientes podem rever o estado de acesso condicional e informações detalhadas sobre os detalhes das políticas que são aplicadas para o início de sessão e o resultado para cada política.

**P: Como posso começar a utilizar?**

**R:** Para começar:

* Navegue para o relatório de inícios de sessão na [portal do Azure](https://portal.azure.com).
* Clique no início de sessão que pretende resolver problemas.
* Navegue para o **acesso condicional** separador. Aqui, pode ver todas as políticas que afetou o início de sessão e o resultado para cada política. 
    
**P: Quais são todos os valores possíveis para o estado de acesso condicional?**

**R:** Estado do acesso condicional pode ter os seguintes valores:

* **Não foram aplicados**: Isso significa que não havia nenhuma política de acesso condicional com o utilizador e a aplicação no âmbito. 
* **Success**: Isso significa que não havia uma política de acesso condicional com o utilizador e a aplicação no âmbito e políticas de acesso condicional serem satisfeitas com êxito. 
* **Falha de**: Isso significa que não havia uma política de acesso condicional com o utilizador e a aplicação no âmbito e políticas de acesso condicional não foram satisfeitas. 
    
**P: Quais são todos os valores possíveis para o resultado de política de acesso condicional?**

**R:** Uma política de acesso condicional pode ter os seguintes resultados:

* **Success**: A política com êxito foi cumprida.
* **Falha de**: A política não foi cumprida.
* **Não foram aplicados**: Isso pode ser uma vez que não cumpria as condições de política.
* **Não ativado**: Isso se deve a política no estado desativado. 
    
**P: O nome da política do relatório de todos os início de sessão não corresponde ao nome de política na AC. Porquê?**

**R:** O nome da política do relatório de todos os início de sessão se baseia no nome da política de AC no momento da entrada. Isso pode ser inconsistente com o nome da política na AC se atualizou o nome da política mais tarde, ou seja, após o início de sessão.

**P: Meu início de sessão foi bloqueado devido a uma política de acesso condicional, mas o relatório de atividade de início de sessão mostra que o início de sessão foi concluída com êxito. Porquê?**

**R:** Atualmente o relatório de início de sessão não pode mostrar os resultados precisos para cenários do Exchange ActiveSync quando o acesso condicional é aplicado. Pode haver casos quando o resultado de início de sessão no relatório mostra um início de sessão-in concluída com êxito, mas o início de sessão, na verdade, falhou devido a uma política de acesso condicional. 
