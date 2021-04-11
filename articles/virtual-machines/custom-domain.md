---
title: Criar e utilizar um domínio personalizado
description: Ligue um domínio personalizado a uma máquina virtual em Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250998"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Adicionar domínio personalizado ao Azure VM ou recurso

Em Azure existem várias formas de ligar um domínio personalizado ao seu VM ou recurso. Para qualquer recurso com um IP público (Máquina Virtual, Balanceador de Carga, Gateway de Aplicação) a forma mais direta é criar um registo A estabelecido no seu registo de domínio correspondente. 

## <a name="prerequisites"></a>Pré-requisitos 
- Precisa de um VM com um servidor web a funcionar. Pode utilizar o [Quickstart](./linux/quick-create-cli.md) para criar um VM e adicionar NGINX.

- O VM deve estar acessível à web (porta aberta 80, ou 443 ). Para uma implementação mais segura, coloque primeiro o seu VM atrás de um equilibrador de carga ou gateway de aplicação. Para obter mais informações, consulte [Quickstart: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Tenha um domínio existente e acesso às definições de DNS. Para mais informações, consulte [Comprar um domínio personalizado para o Serviço de Aplicações Azure.](../app-service/manage-custom-dns-buy-domain.md)


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Adicionar domínio personalizado ao endereço IP público VM

Quando cria uma máquina virtual no portal Azure, é automaticamente criado um recurso IP público para a máquina virtual. O seu endereço IP público é apresentado na página geral do VM. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Mostra o endereço IP público na secção essencial da página geral do VM.":::

Se selecionar o endereço IP, poderá ver mais informações sobre o mesmo. Verifique se a sua **atribuição de IP** está definida para **estática**. Um endereço IP estático não será alterado se o VM ou o recurso reiniciar ou desligar.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Mostra a configuração IP pública para que possa ver se o endereço IP está estático.":::

Se o seu Endereço IP não estiver estático, terá de criar um FQDN. 

1. Selecione o seu VM no portal. 
1. No menu esquerdo, selecione **Propriedades**
1. No **endereço IP público\etiqueta do nome DNS,** selecione o seu endereço IP.
2. Na **etiqueta de nome DNS,** introduza o prefixo que pretende utilizar.
3. **Selecione Guardar** no topo da página.
4. Selecione **visão geral** no menu esquerdo para voltar à lâmina geral VM.
5. Verifique se o *nome DNS* aparece corretamente. 

Abra um browser e insira o seu endereço IP ou FQDN e verifique se mostra o conteúdo web em execução no seu VM.
 
Depois de verificar o seu IP estático ou FQDN, vá ao seu fornecedor de domínio e navegue para as definições de DNS.

Uma vez lá, adicione um *registo A* que aponta para o seu Endereço IP Público ou FQDN. Por exemplo, o procedimento para a entidade de registo de domínios GoDaddy é o seguinte:
1. Inicie sessão e selecione o domínio personalizado que pretende utilizar.
2. Na secção **Domínios,** selecione **'Gerir Tudo'** e, em seguida, selecione **DNS | Gerir zonas**.
3. Em **Domain Name** (Nome de Domínio), introduza o domínio personalizado e selecione **Search** (Pesquisar).
4. Na página dns Management, selecione Adicionar e, em seguida, selecione A na lista Tipo.
5. Complete os campos da entrada A:
    - Tipo: Deixe **A** selecionada.
    - Anfitrião: Entrar **@**
    - Pontos para: Introduza o Endereço IP Público ou FQDN do seu VM. 
    - Deixar uma hora selecionada.
6. Selecione **Guardar**.

A entrada de registo A é adicionada à tabela de registos DNS.
 
Após a criação do registo, geralmente demora cerca de uma hora para a propagação do DNS, mas às vezes pode demorar até 48 horas. 


 
## <a name="next-steps"></a>Passos seguintes
[Visão geral da rescisão do TLS e fim do fim do TLS com o Gateway de aplicação](../application-gateway/ssl-overview.md).

 
