---
title: Integrar Azure Key Vault com Azure Policy
description: Saiba como criar, recuperar e excluir segredos de um cofre de chaves do Azure usando a biblioteca de cliente .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/12/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: b59b367d38f95bafe7d3e1320d1365f708dc75dd
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077373"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrar Azure Key Vault com Azure Policy

[Azure Policy](../governance/policy/index.yml) é uma ferramenta de governança que oferece aos usuários a capacidade de auditar e gerenciar seu ambiente do Azure em escala. Azure Policy fornece a capacidade de posicionar o guardrails nos recursos do Azure para garantir que eles estejam em conformidade com as regras de política atribuídas. Ele permite que os usuários realizem auditoria, imposição em tempo real e correção de seu ambiente do Azure. Os resultados das auditorias realizadas pela política estarão disponíveis para os usuários em um painel de conformidade em que poderão ver uma análise de quais recursos e componentes estão em conformidade e quais não são.  Para obter mais informações, consulte a [visão geral do serviço de Azure Policy](../governance/policy/overview.md).

Cenários de uso de exemplo:

- Você deseja melhorar a postura de segurança de sua empresa implementando requisitos de tamanhos mínimos de chave e períodos de validade máximos de certificados nos cofres de chaves da sua empresa, mas não sabe quais equipes serão compatíveis e quais não são. 
- Atualmente, você não tem uma solução para executar uma auditoria em sua organização ou está realizando auditorias manuais do seu ambiente, solicitando que equipes individuais em sua organização relatem sua conformidade. Você está procurando uma maneira de automatizar essa tarefa, executar auditorias em tempo real e garantir a precisão da auditoria.
- Você deseja impor as políticas de segurança da empresa e impedir que as pessoas criem certificados autoassinados, mas não tem uma maneira automatizada de bloquear sua criação. 
- Você deseja relaxar alguns requisitos para suas equipes de teste, mas deseja manter controles rígidos em seu ambiente de produção. Você precisa de uma maneira automatizada simples para separar a imposição de seus recursos. 
- Você deseja ter certeza de que pode reverter a imposição de novas políticas no caso de um problema de site ativo. Você precisa de uma solução com um clique para desativar a imposição da política. 
- Você está contando com uma solução de terceiros para auditar seu ambiente e deseja usar uma oferta interna da Microsoft. 

## <a name="types-of-policy-effects-and-guidance"></a>Tipos de efeitos e diretrizes de política

**Auditoria**: quando o efeito de uma política é definido como auditoria, a política não causará alterações significativas em seu ambiente. Ele só alertará você sobre componentes como certificados que não estão em conformidade com as definições de política dentro de um escopo especificado, marcando esses componentes como sem conformidade no painel de conformidade de política. Auditoria será padrão se nenhum efeito de política for selecionado. 

**Deny**: quando o efeito de uma política é definido como Deny, a política bloqueará a criação de novos componentes, como certificados, além de bloquear novas versões de componentes existentes que não estão em conformidade com a definição de política. Os recursos não compatíveis existentes em um cofre de chaves não são afetados. Os recursos de "auditoria" continuarão a funcionar.

## <a name="available-built-in-policy-definitions"></a>Definições de política "internas" disponíveis

Key Vault criou um conjunto de políticas, que você pode atribuir para cenários comuns para gerenciar certificados. Essas políticas são "internas", o que significa que elas não exigem que você escreva nenhum JSON personalizado para habilitá-las e elas estão disponíveis no portal do Azure para você atribuir. Você ainda pode personalizar determinados parâmetros para atender às necessidades da sua organização. 

As oito políticas de visualização são as seguintes.

### <a name="manage-certificate-validity-period-preview"></a>Gerenciar período de validade do certificado (versão prévia)

Essa política permite que você gerencie o período de validade máximo de seus certificados armazenados no cofre de chaves. É uma boa prática de segurança limitar o período máximo de validade de seus certificados. Se uma chave privada do seu certificado fosse comprometida sem detecção, o uso de certificados de vida curta minimiza o intervalo de tempo para danos contínuos e reduz o valor do certificado a um invasor. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Gerenciar tipos de chave de certificado permitidos (versão prévia)
Essa política permite restringir o tipo de certificados que podem estar no cofre de chaves. Você pode usar essa política para verificar se as chaves privadas do certificado são RSA, ECC ou se são apoiadas pelo HSM. Você pode escolher na lista a seguir quais tipos de certificado são permitidos.
- RSA
- RSA – HSM
- ControlCenter 
- ECC-HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Gerenciar gatilhos de ação de tempo de vida do certificado (versão prévia)

Essa política permite que você gerencie a ação de tempo de vida especificada para certificados que estão dentro de um determinado número de dias de sua expiração ou que atingiram uma determinada porcentagem de sua vida útil. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Gerenciar certificados emitidos por uma AC integrada (versão prévia)

Se você usar uma autoridade de certificação integrada Key Vault (DigiCert ou GlobalSign) e desejar que os usuários usem um ou qualquer um desses provedores, poderá usar essa política para auditar ou impor sua seleção. Essa política também pode ser usada para auditar ou negar a criação de certificados autoassinados no Key Vault. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Gerenciar certificados emitidos por uma AC integrada (versão prévia)

Se você usar uma autoridade de certificação interna ou uma autoridade de certificação não integrada ao cofre de chaves e desejar que os usuários usem uma autoridade de certificação de uma lista fornecida, você poderá usar essa política para criar uma lista de permissões permitidas de autoridades de certificação pelo nome do emissor. Essa política também pode ser usada para auditar ou negar a criação de certificados autoassinados no Key Vault. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Gerenciar nomes de curva permitidos para certificados de criptografia de curva elíptica (versão prévia)
Se você usar criptografia de curva elíptica ou certificados ECC, poderá personalizar uma lista de nomes de curva permitida da lista abaixo. A opção padrão permite todos os nomes de curva a seguir. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Gerenciar o tamanho mínimo da chave para certificados RSA (versão prévia)
Se você usar certificados RSA, poderá escolher um tamanho mínimo de chave que seus certificados devem ter. Você pode selecionar uma opção na lista abaixo. 
- 2048 bits
- 3072 bits
- 4096 bits

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Gerenciar certificados que estão dentro de um número especificado de dias de expiração (versão prévia)
Seu serviço pode sofrer uma interrupção se um certificado que não está sendo monitorado adequadamente não é girado antes de sua expiração. Essa política é essencial para verificar se os certificados armazenados no cofre de chaves estão sendo monitorados. É recomendável aplicar essa política várias vezes com limites de expiração diferentes, por exemplo, a 180, 90, 60 e limites de 30 dias. Essa política pode ser usada para monitorar e fazer triagem de expiração de certificado em sua organização. 

## <a name="example-scenario"></a>Cenário de Exemplo

Você gerencia um cofre de chaves usado por várias equipes que contêm certificados 100 e deseja certificar-se de que nenhum dos certificados no cofre de chaves seja válido por mais de 2 anos.

1. Você atribui a política [gerenciar período de validade do certificado](#manage-certificate-validity-period-preview) , especifica que o período de validade máximo de um certificado é de 24 meses e define o efeito da política como "auditoria". 
1. Você exibe o [relatório de conformidade no portal do Azure](#view-compliance-results)e descobre que 20 certificados não são compatíveis e são válidos por > 2 anos e os certificados restantes são compatíveis. 
1. Você entra em contato com os proprietários desses certificados e comunica o novo requisito de segurança de que os certificados não podem ser válidos por mais de dois anos. Algumas equipes respondem e 15 dos certificados foram renovados com um período de validade máximo de 2 anos ou menos. Outras equipes não respondem, e você ainda tem 5 certificados não compatíveis em seu cofre de chaves.
1. Você altera o efeito da política atribuída a "Deny". Os 5 certificados não compatíveis não são revogados e continuam a funcionar. No entanto, eles não podem ser renovados com um período de validade maior que 2 anos. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Habilitando e gerenciando uma política de Key Vault por meio do portal do Azure

### <a name="select-a-policy-definition"></a>Selecionar uma definição de política

1. Inicie sessão no portal do Azure. 
1. Pesquise "política" na barra de pesquisa e selecione **política**.

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img1.png)

1. Na janela política, selecione **definições**.

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img2.png)

1. No filtro de categoria, desmarque **selecionar tudo** e selecione **Key Vault**. 

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img3.png)

1. Agora você deve ser capaz de ver todas as políticas disponíveis para visualização pública, por Azure Key Vault. Verifique se você leu e entendeu a seção de diretrizes de política acima e selecionou uma política que deseja atribuir a um escopo.  

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Atribuir uma política a um escopo 

1. Selecione uma política que você deseja aplicar, neste exemplo, a política **gerenciar período de validade do certificado** é mostrada. Clique no botão atribuir no canto superior esquerdo.

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img5.png)
  
1. Selecione a assinatura em que você deseja aplicar a política. Você pode optar por restringir o escopo a um único grupo de recursos em uma assinatura. Se você quiser aplicar a política a toda a assinatura e excluir alguns grupos de recursos, também poderá configurar uma lista de exclusão. Defina o seletor de imposição de política como **habilitado** se você quiser que o efeito da política (auditoria ou negação) ocorra ou **desabilitado** para ativar ou desativar o efeito (auditoria ou negação). 

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img6.png)

1. Clique na guia parâmetros na parte superior da tela para especificar o período máximo de validade em meses que você deseja. Selecione **auditoria** ou **negar** para o efeito da política seguindo as diretrizes nas seções acima. Em seguida, selecione o botão revisar + criar. 

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img7.png)

### <a name="view-compliance-results"></a>Exibir resultados de conformidade

1. Volte para a folha política e selecione a guia conformidade. Clique na atribuição de política para a qual você deseja exibir os resultados de conformidade.

    ![Visão geral de como Azure Key Vault funciona](./media/policy-img8.png)

1. Nessa página, você pode filtrar os resultados por cofres em conformidade ou sem conformidade. Aqui você pode ver uma lista de cofres de chaves sem conformidade dentro do escopo da atribuição de política. Um cofre será considerado não compatível se qualquer um dos componentes (certificados) no cofre não for compatível. Você pode selecionar um cofre individual para exibir os componentes individuais não compatíveis (certificados). 


    ![Visão geral de como Azure Key Vault funciona](./media/policy-img9.png)

1. Exibir o nome dos componentes em um cofre que não está em conformidade


    ![Visão geral de como Azure Key Vault funciona](./media/policy-img10.png)

1. Se você precisar verificar se os usuários estão sendo negados à capacidade de criar recursos no Key Vault, clique na guia **eventos de componente (versão prévia)** para exibir um resumo das operações de certificado negadas com o solicitante e os carimbos de data/hora das solicitações. 


    ![Visão geral de como Azure Key Vault funciona](./media/policy-img11.png)

## <a name="feature-limitations"></a>Limitações de recursos

Atribuir uma política com um efeito de "negação" pode levar até 30 minutos (caso médio) e 1 hora (pior caso) para começar a negar a criação de recursos sem conformidade. A avaliação da política de componentes existentes em um cofre pode levar até 1 hora (caso médio) e 2 horas (pior caso) antes que os resultados da conformidade sejam visíveis na interface do usuário do Portal. Se os resultados de conformidade aparecerem como "não iniciado", talvez seja devido aos seguintes motivos:
- A avaliação da política ainda não foi concluída. A latência de avaliação inicial pode levar até 2 horas no pior cenário. 
- Não há cofres de chaves no escopo da atribuição de política.
- Não há cofres de chaves com certificados dentro do escopo da atribuição de política. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [serviço de Azure Policy](../governance/policy/overview.md)
- Consulte um exemplo: [Key Vault cofres sem pontos de extremidade de rede virtual](../governance/policy/samples/keyvault-no-vnet-rules.md)

