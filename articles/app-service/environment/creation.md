---
title: Criar um Ambiente de Serviço de Aplicações
description: Saiba como criar um Ambiente de Serviço de Aplicações.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 52a43874060748479beebc994bf0a9b663cde9dc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020184"
---
# <a name="create-an-app-service-environment"></a>Criar um Ambiente de Serviço de Aplicações

> [!NOTE]
> Este artigo é sobre o App Service Environment v3 (pré-visualização)
> 

O [App Service Environment (ASE)][Intro] é uma única implantação do Serviço de Aplicações que injeta na sua Rede Virtual Azure (VNet).  O ASEv3 só suporta expor aplicações num endereço privado no seu Vnet. Quando um ASEv3 é criado durante a pré-visualização, estes recursos são adicionados à sua subscrição.

- Ambiente do Serviço de Aplicações
- Ponto final privado

A implantação de um ASE requer a utilização de duas sub-redes.  Uma sub-rede terá o ponto final privado.  Esta sub-rede pode ser usada para outras coisas, como VMs.  A outra sub-rede é utilizada para chamadas de saída feitas a partir do ASE.  Esta sub-rede não pode ser usada para outra coisa que não o ASE. 

## <a name="before-you-create-your-ase"></a>Antes de criar o seu ASE

Depois da criação do seu ASE, não pode mudar:

- Localização
- Subscrição
- Grupo de recursos
- Rede Virtual Azure (VNet) utilizada
- Sub-redes utilizadas
- Tamanho da sub-rede
- Nome do seu ASE

A sub-rede de saída tem de ser suficientemente grande para manter o tamanho máximo que irá escalar o seu ASE. Escolha uma sub-rede suficientemente grande para suportar as suas necessidades de escala máxima, uma vez que não pode ser alterada após a criação. O tamanho recomendado é um /24 com 256 endereços.

Após a criação do ASE, pode adicionar-lhe aplicações. Quando o seu ASEv3 não tem planos de Serviço de Aplicações, há uma taxa como se tivesse tido uma instância de um plano de Serviço de Aplicações I1v2 no seu ASE.  

O ASEv3 só é oferecido em regiões selecionadas. Mais regiões serão adicionadas à medida que a pré-visualização avança em direção à AG. 

## <a name="creating-an-ase-in-the-portal"></a>Criação de um ASE no portal

1. Para criar um ASEv3, procure no mercado do Ambiente de Serviço de **Aplicações (pré-visualização)**.  
2. Fundamentos: Selecione a Subscrição, selecione ou crie o Grupo de Recursos e insira o nome do seu ASE.  O nome ASE também será usado para o sufixo de domínio do seu ASE.  Se o seu nome ASE for *contoso,* então o sufixo de domínio será *contoso.appserviceenvironment.net*.  Este nome será automaticamente definido na sua zona privada Azure DNS utilizada pelo Vnet em que o ASE é implantado. 

    ![App Service Environment cria separador básico](./media/creation/creation-basics.png)

3. Hospedagem: Selecione OS Preference, Host Group deployment. A preferência do SO indica o sistema operativo que utilizará inicialmente para as suas aplicações neste ASE. Pode adicionar aplicações do outro SISTEMA após a criação do ASE. A implementação do Grupo Anfitrião é usada para selecionar hardware dedicado. Com o ASEv3 pode selecionar Enabled e, em seguida, aterrar em hardware dedicado. Você é cobrado por todo o anfitrião dedicado com a criação da ASE e, em seguida, um preço reduzido para as suas instâncias do plano de App Service. 

    ![Seleções de hospedagem de App Service Environment](./media/creation/creation-hosting.png)

4. Rede: Selecione ou crie a sua Rede Virtual, selecione ou crie a sua sub-rede de entrada, selecione ou crie a sua sub-rede de saída. Qualquer sub-rede utilizada para saída deve estar vazia e delegada no Microsoft.Web/hostingEnvironments. Se criar a sua sub-rede através do portal, a sub-rede será automaticamente delegada para si.

    ![Seleções de networking de ambiente de serviço de aplicativos](./media/creation/creation-networking.png)

5. Reveja e Crie: Verifique se a sua configuração está correta e selecione criar. O seu ASE levará aproximadamente uma hora para criar. 

    ![App Service Environment review and create](./media/creation/creation-review.png)

Após a conclusão da sua criação ASE, pode selecioná-la como um local ao criar as suas apps. Para saber mais sobre a criação de apps no seu novo ASE, leia [Usando um Ambiente de Serviço de Aplicações][UsingASE]

## <a name="os-preference"></a>Preferência por OS
Numa ASE pode ter aplicações windows, aplicações Linux ou ambas. No ASEv2, a preferência inicial selecionada durante a criação adiciona a infraestrutura de alta disponibilidade para o SO durante a criação da ASE. Para adicionar aplicações do outro SO, basta fazer as aplicações como de costume e selecionar o SISTEMA que pretende. No ASEv3, isso não afetará o comportamento de backend de forma apreciativa.  

## <a name="dedicated-hosts"></a>Anfitriões dedicados
O ASE é normalmente implantado em VMs que são a provisionados num hipervisor multi-inquilino. Se precisar de implementar em sistemas dedicados, incluindo o hardware, pode aloque o seu ASE a anfitriões dedicados. Na pré-estreia inicial do ASEv3, os anfitriões dedicados vêm em pares. Cada hospedeiro dedicado está numa zona de disponibilidade separada, se a região o permitir. As implementações ase dedicadas ao hospedeiro têm um preço diferente do normal. Há uma taxa para o anfitrião dedicado e, em seguida, outra taxa para cada instância do plano de app.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
