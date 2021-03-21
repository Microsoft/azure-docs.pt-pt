---
title: Controlar o acesso externo aos recursos no Azure Ative Directory com etiquetas de sensibilidade.
description: Utilize etiquetas de sensibilidade como parte do seu plano de segurança global para acesso externo.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565177"
---
# <a name="control-access-with-sensitivity-labels"></a>Controlar o acesso com etiquetas de sensibilidade 

[As etiquetas de sensibilidade](/microsoft-365/compliance/sensitivity-labels) ajudam-no a controlar o acesso ao seu conteúdo nas aplicações do Office 365 e em contentores como microsoft teams, Microsoft 365 Groups e SharePoint sites. Podem proteger o seu conteúdo sem impedir a colaboração e as capacidades de produção dos seus utilizadores. As etiquetas de sensibilidade permitem-lhe enviar os conteúdos da sua organização através de dispositivos, apps e serviços, protegendo os seus dados e cumprindo as suas políticas de conformidade e segurança. 

Com etiquetas de sensibilidade pode:

* **Classifique o conteúdo sem adicionar quaisquer definições de proteção**. Pode atribuir uma classificação a conteúdos (como um autocolante) que persista e vagueie com o seu conteúdo tal como é usado e partilhado. Pode utilizar esta classificação para gerar relatórios de utilização e ver dados de atividade dos seus conteúdos confidenciais.

* **Impor definições de proteção tais como encriptação, marcas de água e restrições de acesso**. Por exemplo, os utilizadores podem aplicar uma etiqueta Confidencial a um documento ou e-mail, e esse rótulo pode [encriptar o conteúdo](/microsoft-365/compliance/encryption-sensitivity-labels) e adicionar uma marca de água "Confidencial". Além disso, pode [aplicar uma etiqueta de sensibilidade a um recipiente](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) como um site sharePoint e impor se os utilizadores externos podem aceder ao conteúdo que contém.

Etiquetas de sensibilidade no e-mail e outros conteúdos viajam com o conteúdo. As etiquetas de sensibilidade nos recipientes podem restringir o acesso ao recipiente, mas o conteúdo do recipiente não herda o rótulo. Por exemplo, um utilizador poderia tirar conteúdo de um site protegido, descarregá-lo e partilhá-lo sem restrições, a menos que o conteúdo também tivesse uma etiqueta de sensibilidade.

 >[!NOTE]
>Para aplicar etiquetas de sensibilidade, os utilizadores devem ser assinados na sua conta de trabalho ou escola da Microsoft. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Permissões necessárias para criar e gerir níveis de sensibilidade

Os membros da sua equipa de compliance que irão criar etiquetas de sensibilidade precisam de permissões para o Microsoft 365 Compliance Center, Microsoft 365 Security Center ou o Security & Compliance Center.

Por defeito, os administradores globais do seu inquilino têm acesso a estes centros administrativos e podem dar acesso a oficiais de conformidade e outras pessoas, sem lhes dar todas as permissões de um administrador inquilino. Para este acesso de administração limitado delegado, adicione os utilizadores ao Administrador de Dados de Conformidade, Ao Administrador de Conformidade ou ao Grupo de Função de Administrador de Segurança.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Determine a sua estratégia de etiqueta de sensibilidade

Ao pensar em governar o acesso externo ao seu conteúdo, determine o seguinte:

**Para todos os conteúdos e contentores**

* Como vai definir o que é Alto, Médio ou Baixo Impacto Empresarial (HBI, MBI, LBI)? Considere o impacto para a sua organização se tipos específicos de conteúdo forem partilhados de forma inadequada.

   * Conteúdo com tipos específicos de conteúdo inerentemente [sensível,](/microsoft-365/compliance/apply-sensitivity-label-automatically)tais como cartões de crédito ou números de passaporte

   * Conteúdo criado por grupos ou pessoas específicas (por exemplo, agentes de conformidade, gestores financeiros ou executivos)

   * Conteúdo em bibliotecas ou sites específicos. Por exemplo, contentores que acolhem estratégia organizacional ou dados financeiros privados

   * Outros critérios

* Que categorias de conteúdo (por exemplo, conteúdo HBI) devem ser restringidas ao acesso por utilizadores externos?

   * As restrições podem incluir ações como restringir o acesso a contentores e encriptar conteúdo.

* Quais os predefinições para dados, sites ou Grupos Microsoft 365?

* Onde utilizará rótulos de sensibilidade para [rotular e monitorizar,](/microsoft-365/compliance/sensitivity-labels)versus para [impor encriptação](/microsoft-365/compliance/encryption-sensitivity-labels) ou para [impor restrições](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)de acesso ao contentor?

**Para e-mail e conteúdo**

* Pretende aplicar [automaticamente rótulos de sensibilidade](/microsoft-365/compliance/apply-sensitivity-label-automatically) ao conteúdo, ou fazê-lo manualmente?

   * Se optar por fazê-lo manualmente, deseja [recomendar aos utilizadores que apliquem uma etiqueta?](/microsoft-365/compliance/apply-sensitivity-label-automatically)

**Para contentores**

* Que critérios determinarão se grupos, equipas ou sites do SharePoint exigem que o acesso seja restringido utilizando etiquetas de sensibilidade?

* Pretende apenas rotular o conteúdo destes contentores ou pretende [rotular automaticamente](/microsoft-365/compliance/apply-sensitivity-label-automatically) os ficheiros existentes no SharePoint e no OneDrive?

Veja estes [cenários comuns para rótulos de sensibilidade](/microsoft-365/compliance/get-started-with-sensitivity-labels) para outras ideias sobre como usar rótulos de sensibilidade.

### <a name="sensitivity-labels-on-email-and-content"></a>Etiquetas de sensibilidade no e-mail e conteúdo

Quando atribui um rótulo de sensibilidade a um documento ou e-mail, é como um carimbo que é aplicado a conteúdos personalizáveis, textos claros e persistentes. 

* Os meios **personalizáveis** podem criar rótulos apropriados para a sua organização e determinar o que acontece quando são aplicados.

* **Texto claro** significa que faz parte dos metadados do item e é legível por aplicações e serviços para que possam aplicar as suas próprias ações de proteção.

* **Persistente** significa que o rótulo e quaisquer proteções associadas vagueiam pelo conteúdo e tornam-se a base para aplicar e aplicar políticas.

 

> [!NOTE]
> Cada item de conteúdo pode ter uma única etiqueta de sensibilidade aplicada a ele.


### <a name="sensitivity-labels-on-containers"></a>Etiquetas de sensibilidade em recipientes

Pode aplicar etiquetas de sensibilidade em recipientes como [os grupos Microsoft 365,](../enterprise-users/groups-assign-sensitivity-labels.md) [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)e [sharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)sites . Quando aplica esta etiqueta de sensibilidade a um recipiente suportado, a etiqueta aplica automaticamente as definições de classificação e proteção ao local ou grupo ligado. As etiquetas de sensibilidade nestes recipientes podem controlar os seguintes aspetos dos contentores:

* **Privacidade**. Pode escolher quem pode ver o site: utilizadores específicos, todos os utilizadores internos, ou qualquer pessoa.

* **Acesso externo ao utilizador**. Controla se o proprietário do grupo pode adicionar hóspedes ao grupo.

* **Acesso a partir de dispositivos não geridos.** Determina se e como os dispositivos não geridos podem aceder ao conteúdo.

 

![Uma imagem de etiquetas de sensibilidade de edição](media/secure-external-access/8-edit-label.png)

 

Quando se aplica um rótulo de sensibilidade a um recipiente como um site sharePoint, este não é aplicado ao conteúdo: etiquetas de sensibilidade nos recipientes controlam o acesso ao conteúdo dentro do recipiente. 

* Se pretender aplicar automaticamente etiquetas ao conteúdo dentro do recipiente, consulte [Aplicar automaticamente uma sensibilidade ao conteúdo](/microsoft-365/compliance/apply-sensitivity-label-automatically).

* Se pretender que os utilizadores possam aplicar manualmente etiquetas neste conteúdo, certifique-se de que [ativou etiquetas de sensibilidade para ficheiros office no SharePoint e OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files).

### <a name="plan-to-implement-sensitivity-labels"></a>Plano para implementar rótulos de sensibilidade

Depois de ter determinado como pretende utilizar etiquetas de sensibilidade e em que conteúdo e sites pretende aplicá-los, consulte a seguinte documentação para o ajudar a executar a sua implementação.

1. [Começar com rótulos de sensibilidade](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Criar uma estratégia de implantação](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Criar e publicar rótulos de sensibilidade](/microsoft-365/compliance/create-sensitivity-labels)

4. [Restringir o acesso ao conteúdo usando etiquetas de sensibilidade para aplicar encriptação](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Use rótulos de sensibilidade com equipas, grupos e sites](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [Permitir etiquetas de sensibilidade para ficheiros office em SharePoint e OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua desejada postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md) (Você está aqui.)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)