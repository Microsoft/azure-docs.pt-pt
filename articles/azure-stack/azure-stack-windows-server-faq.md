---
title: FAQs relacionados com o Azure Stack do Windows Server | Documentos da Microsoft
description: Lista de FAQs do Marketplace do Azure Stack para o Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: b498283ee117935438c55e5162e92acdb0f5ebfe
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649905"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Windows Server na pilha do Azure Marketplace FAQ

Este artigo responde a algumas perguntas mais frequentes sobre as imagens do Windows Server no [do Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Itens do Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Como atualizar uma imagem mais recente do Windows?

Em primeiro lugar, determine se todos os modelos Azure Resource Manager Consulte versões específicas. Se assim for, esses modelos de atualização ou manter as versões mais antigas de imagem. É melhor usar **versão: mais recente**.

Em seguida, se consultar quaisquer conjuntos de dimensionamento de Máquina Virtual para uma versão específica, deve pensar sobre se estas serão redimensionadas mais tarde e decida se pretende manter as versões mais antigas. Se nenhuma dessas condições se aplicarem, elimine imagens mais antigas no Marketplace antes de transferir os mais recentes. Utilize a gestão de mercado para fazer isso, se isso é como o original foi transferido. Em seguida, transfira a versão mais recente.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Quais são as opções de licenciamento para imagens do Marketplace do Windows Server no Azure Stack?

A Microsoft oferece duas versões de imagens do Windows Server através do Marketplace do Azure Stack:

- **Pague à medida que usar**: Estas imagens de executam os medidores de Windows de preço integral.
   Quem deve usar: Os clientes do Enterprise Agreement (EA) que utilizam o *modelo de faturação do consumo*; CSPs que não pretende utilizar o licenciamento de SPLA.
- **Traga a sua própria licença (BYOL)**: Estas imagens de medidores básicas de execução.
   Quem deve usar: Clientes com EA com uma licença do Windows Server; CSPs que utilizam o licenciamento de SPLA.

Benefício de utilização do Azure híbrido (AHUB) não é suportada no Azure Stack. Os clientes que de licenças através do modelo de "Capacidade" tem de utilizar a imagem BYOL. Se estiver a testar com o Azure Stack Development Kit (ASDK), pode utilizar uma destas opções.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>E se eu baixei a versão errada para oferecer meus inquilinos/utilizadores?

Elimine primeiro a versão incorreta por meio do gerenciamento do Marketplace. Aguarde pela conclusão totalmente (Observe as notificações para a conclusão, não o painel de gestão de Marketplace). Em seguida, transfira a versão correta.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>E se meu usuário selecionado a caixa de "Tenho uma licença" de mensagens em fila no Windows anterior incorretamente baseia-se, e elas não tiverem uma licença?

Ver [converter VMs do Windows Server com o benefício de volta para pay as you go](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>E se eu tiver uma imagem mais antiga e meu usuário se esqueceu de verificar a caixa "Tenho uma licença", ou podemos utilizar o nossas próprio imagens e temos a elegibilidade de contrato Enterprise?

Ver [converter uma VM existente com o Azure híbrido benefício para o Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Tenha em atenção que o benefício híbrido do Azure não se aplica ao Azure Stack, mas o efeito desta definição é aplicável.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>E outras VMs que utilizam o Windows Server, como SQL ou Machine Learning Server?

Estas imagens são aplicáveis a **licenseType** parâmetro, para que sejam de pagamento que utiliza. Pode definir este parâmetro (consulte as FAQ anteriores resposta). Isto aplica-se apenas ao software do Windows Server, não para os produtos em camadas, como SQL, que requerem que traga a sua própria licença. Pagamento como utilizar o licenciamento não se aplica a produtos de software em camadas.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Tenho um Enterprise Agreement (EA) e irá utilizar minha licença de servidor do Windows de EA; como me certifico de que imagens são faturadas corretamente?

Pode adicionar **licenseType: Windows_Server** num modelo do Azure Resource Manager. Esta definição tem de ser adicionada a cada bloco de recurso de máquina virtual.

## <a name="activation"></a>Ativação

Para ativar uma máquina virtual do Windows Server no Azure Stack, as seguintes condições devem ser verdadeiras:

- O OEM definiu o marcador BIOS apropriado em todos os sistemas de anfitriões no Azure Stack.
- Windows Server 2012 R2 e Windows Server 2016 têm de utilizar [ativação automática de Máquina Virtual](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Serviço de gestão de chaves (KMS) e outros serviços de ativação não são suportados no Azure Stack.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Como eu posso verificar que a minha máquina virtual está ativada?

Execute o seguinte comando numa linha de comandos elevada:

```shell
slmgr /dlv
```

Se ele está corretamente ativado, verá isso claramente indicado e o nome de anfitrião apresentado no `slmgr` saída. Não dependem de marcas d'água na exibição à medida que eles não podem ser atualizados ou estão a mostrar a partir de outra máquina virtual por trás de seu.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>A minha VM não está configurado para utilizar a AVMA, como posso arrumá-lo?

Execute o seguinte comando numa linha de comandos elevada:

```shell
slmgr /ipk <AVMA key>
```

Consulte a [ativação automática de Máquina Virtual](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) artigo para as chaves a utilizar para a sua imagem.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Posso criar meus próprio imagens do Windows Server, como posso fazer-se de que eles usam AVMA?

É recomendável que executar o `slmgr /ipk` linha de comandos com a chave adequada antes de executar o `sysprep` comando. Em alternativa, incluir a chave AVMA em qualquer arquivo de configuração Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Estou a tentar utilizar a minha imagem do Windows Server 2016 criada no Azure e não é ativar ou usando a ativação do KMS.

Execute o comando `slmgr /ipk`. Imagens do Azure poderão não corretamente reverter para a AVMA, mas se chegarem o sistema de KMS do Azure, eles serão ativados. Recomenda-se que garantir que estas VMs estão definidas para utilizar a AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Posso ter realizado todas essas etapas, mas minhas máquinas virtuais ainda está a ativar não.

Contacte o seu fornecedor de hardware para verificar se os marcadores de BIOS corretos foram instalados.

### <a name="what-about-earlier-versions-of-windows-server"></a>E versões anteriores do Windows Server?

[Ativação automática de Máquina Virtual](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) não é suportado em versões anteriores do Windows Server. Terá de ativar manualmente as VMs.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes:

- [Visão geral do Azure Stack Marketplace](azure-stack-marketplace.md)
- [Transferir itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md)
