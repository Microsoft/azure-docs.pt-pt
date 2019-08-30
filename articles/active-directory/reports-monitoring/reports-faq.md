---
title: Perguntas frequentes sobre relatórios de Azure Active Directory | Microsoft Docs
description: Perguntas frequentes quesitons sobre relatórios de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8c3138b82c7dc4a7217e8cb67448a5d824398ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127012"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Perguntas frequentes sobre Azure Active Directory relatórios

Este artigo inclui respostas para perguntas frequentes sobre relatórios do Azure Active Directory (Azure AD). Para obter mais informações, veja [Relatórios do Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introdução 

**P: Atualmente, uso as `https://graph.windows.net/<tenant-name>/reports/` APIs de ponto de extremidade para extrair relatórios de uso de aplicativos integrados e auditoria do Azure AD em nossos sistemas de relatórios de forma programática. O que devo mudar?**

**R:** Pesquise a [referência de API](https://developer.microsoft.com/graph/) para ver como você pode [usar as APIs para acessar os relatórios de atividade](concept-reporting-api.md). Esse ponto de extremidade tem dois relatórios (**auditoria** e **entradas**) que fornecem todos os dados que você obteve no antigo ponto de extremidade da API. Esse novo ponto de extremidade também tem um relatório de entradas com a licença Azure AD Premium que você pode usar para obter o uso do aplicativo, o uso do dispositivo e as informações de entrada do usuário.

---

**P: Atualmente, uso as `https://graph.windows.net/<tenant-name>/reports/` APIs de ponto de extremidade para efetuar pull de relatórios de segurança do Azure AD (tipos específicos de detecções, como credenciais vazadas ou entradas de endereços IP anônimos) em nossos sistemas de relatórios programaticamente. O que devo mudar?**

**R:** Você pode usar a [API](../identity-protection/graph-get-started.md) de detecções de risco da proteção de identidade para acessar as detecções de segurança por meio do Microsoft Graph. Esse novo formato oferece maior flexibilidade na forma como você pode consultar dados, com filtragem avançada, seleção de campo e muito mais, e padroniza as detecções de risco em um tipo para uma integração mais fácil em SIEMs e outras ferramentas de coleta de dados. Como os dados estão em um formato diferente, você não pode substituir uma nova consulta por suas consultas antigas. No entanto, [a nova API usa Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que é o padrão da Microsoft para tais APIs como O365 ou Azure AD. Portanto, o trabalho necessário pode estender seus investimentos atuais do MS Graph ou ajudá-lo a começar sua transição para essa nova plataforma padrão.

---

**P: Como fazer obter uma licença Premium?**

**R:** Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory.

---

**P: Quanto tempo devo ver os dados das atividades depois de obter uma licença Premium?**

**R:** Se você já tiver dados de atividades como uma licença gratuita, poderá vê-lo imediatamente. Se você não tiver dados, levará um ou dois dias para que os dados sejam exibidos nos relatórios.

---

**P: Posso ver os dados do mês passado depois de obter uma licença do Azure AD Premium?**

**R:** Se você tiver alternado recentemente para uma versão Premium (incluindo uma versão de avaliação), poderá ver dados de até 7 dias inicialmente. Quando os dados são acumulados, você pode ver os dados dos últimos 30 dias.

---

**P: Preciso ser um administrador global para ver as entradas de atividade no portal do Azure ou obter dados por meio da API?**

**R:** Não, você também pode acessar os dados de relatórios por meio do portal ou por meio da API se você for um **leitor de segurança** ou **administrador de segurança** para o locatário. É claro que **os administradores globais** também terão acesso a esses dados.

---


## <a name="activity-logs"></a>Registos de atividade


**P: Qual é a retenção de dados para logs de atividade (auditoria e entradas) no portal do Azure?** 

**R:** A tabela a seguir lista o período de retenção de dados para logs de atividade. Para obter mais informações, consulte [políticas de retenção de dados para relatórios do Azure ad](reference-reports-data-retention.md).

| Relatório                 | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Registos de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Inícios de sessão               | N/A           | 30 dias             | 30 dias             |
| Uso do Azure MFA        | 30 dias       | 30 dias             | 30 dias             |

---

**P: Quanto tempo leva até que eu possa ver os dados da atividade depois de concluir minha tarefa?**

**R:** Os logs de auditoria têm uma latência variando de 15 minutos a uma hora. Os logs de atividade de entrada podem levar de 15 minutos a até 2 horas para alguns registros.

---

**P: Posso obter informações sobre o log de atividades do Office 365 por meio do portal do Azure?**

**R:** Embora a atividade do Office 365 e os logs de atividades do Azure AD compartilhem muitos recursos de diretório, se você quiser uma exibição completa dos logs de atividade do Office 365, vá para o [centro de administração Microsoft 365](https://admin.microsoft.com) para obter informações sobre o log de atividades do Office 365.

---

**P: Quais APIs eu uso para obter informações sobre os logs de atividades do Office 365?**

**R:** Use as [APIs de gerenciamento do office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) para acessar os logs de atividade do Office 365 por meio de uma API.

---

**P: Quantos registros eu posso baixar de portal do Azure?**

**R:** Você pode baixar até 5000 registros do portal do Azure. Os registros são classificados pelo *mais recente* e, por padrão, você obtém os registros 5000 mais recentes.

---

## <a name="risky-sign-ins"></a>Inícios de sessão de risco

**P: Há uma detecção de risco na proteção de identidade, mas não estou vendo a entrada correspondente no relatório de entradas. Isso é esperado?**

**R:** Sim, a proteção de identidade avalia o risco de todos os fluxos de autenticação se interativos ou não. No entanto, todos os relatórios somente de entradas mostram apenas as entradas interativas.

---

**P: Como fazer saber por que uma entrada ou um usuário foi sinalizado como arriscado no portal do Azure?**

**R:** Se você tiver uma assinatura **Azure ad Premium** , poderá saber mais sobre as detecções de risco subjacentes selecionando o usuário em **usuários sinalizados para risco** ou selecionando um registro no relatório de **entradas arriscadas** . Se você tiver uma assinatura **gratuita** ou **básica** , poderá exibir os relatórios dos usuários em risco e de entradas arriscadas, mas não poderá ver as informações de detecção de risco subjacente.

---

**P: Como os endereços IP são calculados no relatório entradas e entradas arriscadas?**

**R:** Os endereços IP são emitidos de forma que não haja nenhuma conexão definitiva entre um endereço IP e onde o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é ainda mais complicado por fatores como provedores móveis e VPNs que emitem endereços IP de pools centrais geralmente estão longe de onde o dispositivo cliente é realmente usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações. 

---

**P: O que significa a detecção de risco "entrar com risco adicional detectado"?**

**R:** Para fornecer informações sobre todas as entradas arriscadas no seu ambiente, as funções "entrar com risco adicional detectado" como espaço reservado para entradas para detecções que são exclusivas para Azure AD Identity Protection assinantes.

---

## <a name="conditional-access"></a>Acesso Condicional

**P: O que há de novo neste recurso?**

**R:** Agora, os clientes podem solucionar problemas de políticas de acesso condicional por meio de todos os relatórios de entradas. Os clientes podem examinar o status de acesso condicional e aprofundar-se nos detalhes das políticas aplicadas à entrada e ao resultado de cada política.

**P: Como fazer começar?**

**R:** Para começar:

* Navegue até o relatório de entradas no [portal do Azure](https://portal.azure.com).
* Clique na entrada que você deseja solucionar problemas.
* Navegue até a guia **acesso condicional** . Aqui, você pode exibir todas as políticas que afetaram a entrada e o resultado de cada política. 
    
**P: Quais são os valores possíveis para o status de acesso condicional?**

**R:** O status de acesso condicional pode ter os seguintes valores:

* **Não aplicado**: Isso significa que não havia nenhuma política de CA com o usuário e o aplicativo no escopo. 
* **Êxito**: Isso significa que houve uma política de CA com o usuário e o aplicativo no escopo e as políticas de CA foram atendidas com êxito. 
* **Falha de**: Isso significa que houve uma política de CA com o usuário e o aplicativo no escopo e as políticas de CA não foram satisfeitas. 
    
**P: Quais são os valores possíveis para o resultado da política de acesso condicional?**

**R:** Uma política de acesso condicional pode ter os seguintes resultados:

* **Êxito**: A política foi satisfeita com êxito.
* **Falha de**: A política não foi satisfeita.
* **Não aplicado**: Isso pode ocorrer porque as condições da política não atenderam.
* **Não habilitado**: Isso ocorre devido à política em estado desabilitado. 
    
**P: O nome da política no relatório de entrada All não corresponde ao nome da política na CA. Porquê?**

**R:** O nome da política no relatório de entrada é baseado no nome da política da autoridade de certificação no momento da entrada. Isso pode ser inconsistente com o nome da política na AC se você atualizou o nome da política posteriormente, ou seja, após a entrada.

**P: Minha entrada foi bloqueada devido a uma política de acesso condicional, mas o relatório de atividade de entrada mostra que a entrada foi bem-sucedida. Porquê?**

**R:** No momento, o relatório de entrada pode não mostrar resultados precisos para cenários do Exchange ActiveSync quando o acesso condicional é aplicado. Pode haver casos em que o resultado da entrada no relatório mostra uma entrada bem-sucedida, mas a entrada realmente falhou devido a uma política de acesso condicional. 
