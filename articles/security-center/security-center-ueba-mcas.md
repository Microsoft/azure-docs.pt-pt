---
title: Deteção no Centro de segurança do Azure de ameaças | Documentos da Microsoft
description: " Detetar ameaças e malicioso ao integrar o Microsoft Cloud App Security com o Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 7ffb9684045031c5bca7a79a15db7cb16fc99e9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332402"
---
# <a name="ueba-for-azure-resources-and-users"></a>UEBA para utilizadores e recursos do Azure 

Parceiros do Centro de segurança do Azure com o Microsoft Cloud App Security para lhe oferecer alertas com base no utilizador e a análise comportamental de entidade (UEBA) para os seus recursos do Azure e os utilizadores (atividade do Azure). Estes alertas detetam anomalias no comportamento dos utilizadores e baseiam-se na análise comportamental de utilizadores e entidades e na aprendizagem automática (ML) para que possa executar de imediato a deteção de ameaças avançada nas atividades das suas subscrições. Uma vez que são ativadas automaticamente, as novas deteções de anomalias fornecem resultados imediatos ao disponibilizar deteções imediatas, ao segmentarem diversas anomalias comportamentais nos utilizadores e nos recursos associados à sua subscrição. Além disso, estes alertas tiram partido de dados adicionais que já existem no motor de deteção da Microsoft Cloud App Security, para o ajudar a acelerar o processo de investigação e a conter as ameaças permanentes. 

> [!NOTE]
> Centro de segurança do Azure, que pode armazenar uma cópia dos dados relacionados com a segurança dos clientes, recolhidos a partir de ou associado um recurso de cliente (por exemplo, a máquina virtual ou a Azure Active Directory inquilino): (a) na mesma geografia que esse recurso, exceto nessas áreas geográficas onde Microsoft ainda não foi que implementar o Centro de segurança do Azure, caso uma cópia de tais dados será armazenado nos Estados Unidos; e (b) em que o Centro de segurança do Azure utiliza o outro serviço Online da Microsoft para processar esses dados, pode armazenar esses dados em conformidade com as regras de localização geográfica de outros serviços Online.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Ativado válido, [licença do Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [Escalão Standard do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Alertas de deteção de ameaças

Centro de segurança suporta alertas de deteção de anomalias do Cloud App Security, tais como:

**Deslocação impossível**
-  Esta deteção identifica duas atividades de utilizador (é uma única ou múltiplas sessões) provenientes de localizações distantes num período de tempo mais curto do que o tempo demoraria viajam de primeira localização para o segundo, que indica que o utilizador que um utilizador diferente está a utilizar as mesmas credenciais. Esta deteção tira partido de um algoritmo de machine learning que ignora "positivos falsos óbvios" que contribuem para a condição de deslocação impossível, tais como as VPNs e localizações regularmente utilizadas por outros utilizadores na organização. A deteção tem um período de aprendizagem inicial de sete dias durante o qual aprende a identificar o padrão de atividade de um novo utilizador.

**Atividade de país pouco frequente**
- Esta deteção considera-se em localizações de atividade para determinar localizações novas e pouco frequentes. O motor de deteção de anomalias armazena informações sobre localizações anteriores utilizadas por utilizadores na organização. Um alerta é acionado quando ocorre uma atividade a partir de uma localização que não foi recentemente ou nunca visitada por nenhum utilizador da organização. 

**Atividade a partir de endereços IP anónimos**
- Esta deteção identifica que os utilizadores estavam ativos de um endereço IP que foi identificado como um endereço IP de proxy anónimo. Estes proxies são utilizados por pessoas que pretendem ocultar o endereço IP do seu dispositivo e podem ser utilizadas para más intenções. Esta deteção tira partido de um algoritmo de machine learning que reduz o "falsos positivos", como endereços IP marcados incorretamente, que são amplamente utilizados por utilizadores na organização.
 
  ![Alerta de deteção de ameaças](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Desativar alertas de deteção de ameaças

Estes alertas são ativados por predefinição, mas pode desativá-las:

1. No painel do Centro de segurança, selecione **política de segurança**. Para a subscrição que pretende alterar, clique em **editar as definições de**.
2.  Clique em **deteção de ameaças**.
3. Sob **permitir integrações**, desmarque a opção **permitir que o Microsoft Cloud App Security a aceder aos meus dados**e clique em **guardar**.

   ![Alerta de deteção de ameaças](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Existe um período de aprendizagem inicial de sete dias durante os quais nem todas as anomalias são geradas alertas de deteção. Depois disso, cada sessão é comparada com a atividade, quando os utilizadores estavam ativos, endereços IP, dispositivos, entre outros fatores detetados no último mês e com a pontuação de risco dessas atividades. Estas deteções fazem parte da máquina do motor de deteção de anomalias de aprendizagem que perfis de seu ambiente e aciona alertas relativamente a uma linha de base que foi aprendida na atividade da sua organização. Estas deteções também tirar partido de algoritmos de machine learning concebidos para criar um perfil a utilizadores e o padrão de logon para reduzir os falsos positivos.
>
  
## <a name="next-steps"></a>Passos Seguintes
Este artigo mostrou-lhe trabalhar com a deteção de ameaças no Centro de segurança do Azure. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
