---
title: Sobre o Azure Lab Services | Microsoft Docs
description: Saiba como o Lab Services pode tornar mais fácil criar, gerir e proteger laboratórios com máquinas virtuais que podem ser utilizadas pelos programadores, técnicos de teste, educadores, estudantes, entre outros.
ms.topic: overview
ms.date: 09/16/2020
ms.openlocfilehash: ad17ebb3a803a15d1ac9ef8cb71cf8ca7976243b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91333941"
---
# <a name="an-introduction-to-azure-lab-services"></a>Introdução ao Azure Lab Services
**A Azure Lab Services** permite criar laboratórios cuja infraestrutura é gerida pela Azure. Atualmente, o laboratório de sala de aula é o único tipo de laboratório gerido que é apoiado pelos Serviços de Laboratório Azure. O próprio serviço trata de toda a gestão da infraestrutura para um tipo de laboratório gerido, desde girar VMs até lidar com erros e escalar a infraestrutura. Depois de um administrador de TI criar uma conta de laboratório nos Serviços Azure Lab, um instrutor pode rapidamente configurar um laboratório para a aula, especificar o número e o tipo de VMs que são necessários para exercícios na classe, e adicionar utilizadores à classe. Uma vez que um utilizador se registe na classe, o utilizador pode aceder ao VM para fazer exercícios para a classe.  

## <a name="key-capabilities"></a>Principais capacidades
O Azure Lab Services suporta as seguintes funcionalidades-chave:

- **Configuração rápida e flexível de um laboratório**. Ao utilizar o Azure Lab Services, os proprietários do laboratório podem configurar rapidamente um laboratório para as suas necessidades. O serviço oferece a opção de cuidar de todos os trabalhos de infraestrutura Azure para tipos de laboratório geridos. O serviço fornece dimensionamento incorporado e resiliência da infraestrutura para laboratórios que o serviço gere automaticamente.
- **Experiência simplificada para utilizadores do laboratório**. Os utilizadores do laboratório podem registar-se num laboratório com um código de registo e podem aceder ao laboratório a qualquer momento para usar os recursos do laboratório. 
- **Análise e otimização de custos**. O proprietário do laboratório pode definir agendas de laboratório para encerrar e iniciar automaticamente as máquinas virtuais. O proprietário do laboratório pode definir um horário para especificar as faixas horárias quando as máquinas virtuais do laboratório são acessíveis aos utilizadores e definir políticas de utilização por utilizador ou por laboratório para otimizar o custo. 

Se quiser inserir o que precisa num laboratório e deixar o serviço configurar e gerir a infraestrutura necessária para o laboratório, escolha entre um dos tipos de **laboratório geridos.** Atualmente, o **laboratório de sala de aula** é o único tipo de laboratório gerido que pode criar com os Serviços de Laboratório Azure.

As seguintes secções fornecem mais detalhes sobre estes laboratórios. 

## <a name="managed-lab-types"></a>Tipos de laboratório geridos
O Azure Lab Services permite-lhe criar laboratórios cuja infraestrutura é gerida pelo Azure. Este artigo refere-se a eles como tipos de laboratório geridos. Os tipos de laboratório geridos oferecem diferentes tipos de laboratórios que se adequam à sua necessidade específica. Atualmente, o único tipo de laboratório gerido que é suportado é **o laboratório de sala de aula.** 

Os tipos de laboratório geridos permitem-lhe começar imediatamente, com a configuração mínima. O próprio serviço trata de toda a gestão da infraestrutura para o laboratório, desde girar os VMs até lidar com erros e escalar a infraestrutura. Para criar um laboratório gerido, como um laboratório de sala de aula, é preciso criar uma conta de laboratório para a sua organização primeiro. A conta de laboratório serve como a conta central em que são geridos todos os laboratórios na organização. 

Quando cria e utiliza recursos Azure nestes tipos de laboratório geridos, o serviço cria e gere recursos em subscrições internas da Microsoft. Não são criados na sua própria subscrição do Azure. O serviço mantém um registo da utilização destes recursos nas subscrições internas da Microsoft. A utilização é faturada na sua subscrição do Azure que contém a conta de laboratório.   

Aqui estão alguns dos casos de **utilização para tipos de laboratório geridos:** 

- Forneça aos estudantes um laboratório com máquinas virtuais configuradas com o que é necessário para uma aula. Dê a cada estudante um número limitado de horas para utilizar as VMs para trabalhos de casa ou projetos pessoais.
- Configure um conjunto de VMs de computação de alto desempenho para realizar investigações repletas de gráficos e computação. Execute as VMs necessárias e limpe as máquinas quando terminar. 
- Mova o laboratório de computadores físico da escola para a cloud. Dimensione automaticamente o número de VMs apenas para o limite máximo de custo e utilização definido no laboratório.  
- Aprovisione rapidamente um laboratório de máquinas virtuais para alojar um hackathon. Elimine o laboratório com um único clique quando terminar. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes tutoriais para instruções passo a passo para criar uma conta de laboratório e criar um laboratório de sala de aula.

- [Tutorial: configurar uma conta de laboratório](tutorial-setup-lab-account.md)
- [Tutorial: criar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)
