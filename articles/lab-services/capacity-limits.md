---
title: Limites de capacidade nos Serviços Azure Lab
description: Saiba mais sobre os limites de capacidade (limites de máquina virtual) nos Serviços Azure Lab.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 92bdc714d70b3d73ca2cbc76b1f5dc5366582cbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444102"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Limites de capacidade nos Serviços Azure Lab
A Azure Lab Services tem limites de capacidade em subscrições Azure para aderir às limitações de quota da Azure Compute e para mitigar a fraude. Todas as subscrições do Azure terão um limite inicial de capacidade, que pode variar em função do tipo de subscrição, número de núcleos computacional padrão e núcleos de GPU disponíveis dentro dos Serviços Azure Lab. Restringe quantas máquinas virtuais pode criar dentro dos seus laboratórios antes de pedir um aumento de limite.  

Se estiver perto ou tiver atingido o limite de núcleos de máquinas virtuais da sua subscrição, verá mensagens dos Serviços Azure Lab quando tentar executar ações que criem máquinas virtuais adicionais. Por exemplo: 

- Criar um laboratório
- Publicar um laboratório
- Ajustar a capacidade do laboratório para adicionar mais máquinas virtuais a um laboratório existente

Estas ações também podem ser desativadas se já tiver atingido o limite dos núcleos. 

![Limites do núcleo - mensagem de aviso](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Assinaturas com limite padrão de núcleos zero
Alguns tipos de subscrição rara que são mais comumente usados para fraude podem ter um limite padrão de 0 núcleos padrão e 0 núcleos de GPU. Se estiver a utilizar um destes tipos de subscrição, o administrador que cria a sua conta de laboratório terá de solicitar um aumento limite antes de poder utilizar os Serviços Azure Lab. 

O administrador pode seguir estas etapas para solicitar um aumento de limite:  

1.  Na sua subscrição, [crie uma conta de laboratório.](tutorial-setup-lab-account.md)
2.  Na página **geral** da conta do laboratório, clique no botão **de aumento do limite de pedido** na parte superior. 
3.  Siga os passos no formulário para apresentar um pedido de apoio para aumentar o limite.

## <a name="request-a-limit-increase"></a>Solicitar um aumento de limite
Se atingir o limite dos núcleos, pode solicitar um aumento limite para continuar a utilizar os Serviços Azure Lab. O processo de pedido é um ponto de verificação para garantir que a sua subscrição não esteja envolvida em casos de fraude ou implantações não intencionais e repentinas em larga escala.

As mensagens sobre o limite de núcleos de máquinas virtuais no portal Azure Lab Services inclui um link para solicitar um aumento de limite. O link abre um novo separador de navegador onde pode criar um novo pedido de suporte. A informação do tipo de emissão, subscrição e tipo de quota será automaticamente preenchida para si, como mostra a seguinte imagem: 

![Novo pedido de suporte](./media/capacity-limits/new-support-request.png)


Em seguida, será solicitado a fornecer mais informações sobre o aumento do limite. No campo **Descrição,** forneça os seguintes detalhes:

- O que está a tentar fazer (por exemplo, criar um laboratório para ensinar uma aula de Ciências da Computação, executar um hackathon, e assim por diante.)
- Tamanho da máquina virtual que está a usar para este laboratório
- Número de máquinas virtuais que precisa

Assim que submeter o pedido de apoio, iremos rever o pedido. Se necessário, entraremos em contato consigo para obter mais detalhes. 

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo:
- [Perguntas frequentes.](classroom-labs-faq.md)