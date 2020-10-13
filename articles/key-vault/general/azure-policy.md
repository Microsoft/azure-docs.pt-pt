---
title: Integrar o Azure Key Vault no Azure Policy
description: Saiba como integrar o Azure Key Vault com a política Azure
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7ef41516d516ce6498fc8c502a229084acdebfa1
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875531"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrar o Azure Key Vault no Azure Policy

[A Azure Policy](../../governance/policy/index.yml) é uma ferramenta de governação que dá aos utilizadores a capacidade de auditar e gerir o seu ambiente Azure em escala. A Azure Policy fornece a capacidade de colocar guarda-costas nos recursos da Azure para garantir que estão em conformidade com as regras políticas atribuídas. Permite que os utilizadores realizem auditorias, execução em tempo real e reparação do seu ambiente Azure. Os resultados das auditorias realizadas por política estarão disponíveis para os utilizadores num painel de conformidade onde poderão ver um aprofundamento dos recursos e componentes que estão em conformidade e que não estão.  Para mais informações, consulte a [visão geral do serviço Azure Policy](../../governance/policy/overview.md).

Exemplo de cenários de utilização:

- Pretende melhorar a postura de segurança da sua empresa implementando requisitos em torno de tamanhos mínimos de chaves e períodos máximos de validade dos certificados nos cofres-chave da sua empresa, mas não sabe quais as equipas que serão compatíveis e quais não. 
- Atualmente não tem uma solução para realizar uma auditoria em toda a sua organização, ou está a realizar auditorias manuais do seu ambiente, pedindo às equipas individuais da sua organização que reportem a sua conformidade. Procura uma forma de automatizar esta tarefa, realizar auditorias em tempo real e garantir a exatidão da auditoria.
- Quer impor as suas políticas de segurança da empresa e impedir que os indivíduos criem certificados auto-assinados, mas não tem uma forma automatizada de bloquear a sua criação. 
- Você quer relaxar alguns requisitos para as suas equipas de teste, mas você quer manter controlos apertados sobre o seu ambiente de produção. Precisa de uma forma simples e automatizada de separar a aplicação dos seus recursos. 
- Você quer ter certeza de que você pode reverter a aplicação de novas políticas em caso de uma questão de site ao vivo. Precisa de uma solução de um clique para desativar a aplicação da apólice. 
- Está a contar com uma solução de terceiros para auditar o seu ambiente e pretende utilizar uma oferta interna da Microsoft. 

## <a name="types-of-policy-effects-and-guidance"></a>Tipos de efeitos e orientações políticas

**Auditoria**: Quando o efeito de uma política estiver definido para a auditoria, a política não provocará alterações no seu ambiente. Irá apenas alertá-lo para componentes como certificados que não cumpram as definições de política dentro de um âmbito especificado, marcando estes componentes como incompatíveis no painel de conformidade da política. A auditoria é padrão se não for selecionado nenhum efeito de política. 

**Negar**: Quando o efeito de uma política for desatado, a política bloqueará a criação de novos componentes, tais como certificados, bem como bloqueará novas versões de componentes existentes que não cumpram a definição de política. Os recursos existentes não conformes dentro de um cofre chave não são afetados. As capacidades de auditoria continuarão a funcionar.

## <a name="available-built-in-policy-definitions"></a>Definições de política "incorporadas" disponíveis

Key Vault criou um conjunto de políticas, que pode atribuir para cenários comuns para gerir certificados. Estas políticas são 'Built-In', o que significa que não exigem que escreva qualquer JSON personalizado para as habilitar e estão disponíveis no portal Azure para que possa atribuir. Você ainda pode personalizar certos parâmetros para se adequar às necessidades da sua organização. 

As oito políticas de pré-visualização são as seguintes.

### <a name="manage-certificate-validity-period-preview"></a>Gerir o período de validade do certificado (pré-visualização)

Esta política permite-lhe gerir o período máximo de validade dos seus certificados armazenados no cofre de chaves. É uma boa prática de segurança limitar o período máximo de validade dos seus certificados. Se uma chave privada do seu certificado ficar comprometida sem ser detetada, a utilização de certificados de curta duração minimiza o prazo para danos em curso e reduz o valor do certificado a um intruso. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Gerir tipos de chave de certificado permitidos (pré-visualização)
Esta política permite-lhe restringir o tipo de certificados que podem estar no seu cofre chave. Pode utilizar esta política para se certificar de que as chaves privadas do certificado são RSA, ECC ou são apoiadas pelo HSM. Pode escolher entre a seguinte lista quais os tipos de certificados permitidos.
- RSA
- RSA - HSM
- ECC 
- ECC - HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Gerir os gatilhos de ação vitalícia do certificado (pré-visualização)

Esta política permite-lhe gerir a ação vitalícia especificada para certificados que se encontram dentro de um determinado número de dias após a sua expiração ou que atingiram uma certa percentagem da sua vida útil. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Gerir certificados emitidos por uma AC integrada (pré-visualização)

Se utilizar uma autoridade de certificados integrados Key Vault (Digicert ou GlobalSign) e pretender que os utilizadores utilizem um ou qualquer um destes fornecedores, pode utilizar esta política para auditar ou fazer cumprir a sua seleção. Esta política também pode ser utilizada para auditar ou negar a criação de certificados auto-assinados no cofre-chave. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Gerir certificados emitidos por uma AC integrada (pré-visualização)

Se utilizar uma autoridade de certificados interno ou uma autoridade de certificados não integrada com cofre-chave e pretender que os utilizadores utilizem uma autoridade de certificados a partir de uma lista que forneça, pode utilizar esta política para criar uma lista permitida de autoridades de certificados por nome emitente. Esta política também pode ser utilizada para auditar ou negar a criação de certificados auto-assinados no cofre-chave. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Gerir nomes de curvas permitidos para certificados de criptografia de curva elíptica (pré-visualização)
Se utilizar a criptografia da curva elíptica ou os certificados ECC, pode personalizar uma lista permitida de nomes de curvas da lista abaixo. A opção predefinição permite todos os seguintes nomes de curvas. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Gerir o tamanho mínimo da chave para certificados RSA (pré-visualização)
Se utilizar certificados RSA, pode escolher um tamanho mínimo de chave que os seus certificados devem ter. Pode selecionar uma opção na lista abaixo. 
- Bit de 2048
- 3072 bit
- 4096 bit

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Gerir certificados que se encontram dentro de um número especificado de dias de expiração (pré-visualização)
O seu serviço pode experimentar uma paragem se um certificado que não está a ser monitorizado adequadamente não for rodado antes da sua expiração. Esta política é fundamental para garantir que os seus certificados armazenados no cofre chave estão a ser monitorizados. Recomenda-se que aplique esta política várias vezes com diferentes limiares de validade, por exemplo, a limiares de 180, 90, 60 e 30 dias. Esta política pode ser usada para monitorizar e triagem expiração de certificado na sua organização. 

## <a name="example-scenario"></a>Cenário de Exemplo

Gere um cofre usado por várias equipas que contém 100 certificados, e quer ter a certeza de que nenhum dos certificados no cofre da chave é válido por mais de 2 anos.

1. Atribui a política de [prazo de validade](#manage-certificate-validity-period-preview) do certificado, especifica que o prazo máximo de validade de um certificado é de 24 meses, e define o efeito da apólice para "auditoria". 
1. Veja o [relatório de conformidade no portal Azure](#view-compliance-results)e descubra que 20 certificados são incompatíveis e válidos por > 2 anos, e os restantes certificados são conformes. 
1. Contacte os proprietários destes certificados e comunique o novo requisito de segurança de que os certificados não podem ser válidos por mais de 2 anos. Algumas equipas respondem e 15 dos certificados foram renovados com um período máximo de validade de 2 anos ou menos. Outras equipas não respondem, e ainda tens 5 certificados não conformes no cofre.
1. Muda-se o efeito da política que atribuiu a "negar". Os 5 certificados não conformes não são revogados e continuam a funcionar. No entanto, não podem ser renovados com um período de validade superior a 2 anos. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Habilitar e gerir uma política de Cofre-Chave através do portal Azure

### <a name="select-a-policy-definition"></a>Selecione uma definição de política

1. Inicie sessão no portal do Azure. 
1. Procure "Política" na Barra **Policy**de Pesquisa e Selecione.

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img1.png)

1. Na janela Política, selecione **Definições**.

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img2.png)

1. No filtro de categoria, **desescolh-se de tudo** e selecione o Cofre de **Chaves**. 

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img3.png)

1. Agora você deve ser capaz de ver todas as políticas disponíveis para visualização pública, para Azure Key Vault. Certifique-se de que leu e compreendeu a secção de orientação de política acima e selecione uma política que pretende atribuir a um âmbito.  

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Atribuir uma Política a um Âmbito 

1. Selecione uma política que pretende aplicar, neste exemplo, é apresentada a política do Período de Validade do **Certificado de Gestão.** Clique no botão de atribuição no canto superior esquerdo.

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img5.png)
  
1. Selecione a subscrição onde deseja que a apólice seja aplicada. Pode optar por restringir o âmbito a apenas um grupo de recursos dentro de uma subscrição. Se pretender aplicar a política a toda a subscrição e excluir alguns grupos de recursos, também pode configurar uma lista de exclusão. Desacorra o seletor de execução da política para **Ativar** se pretender que o efeito da política (auditoria ou negação) ocorra ou **desative** o efeito (auditoria ou negação). 

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img6.png)

1. Clique no separador parâmetros na parte superior do ecrã para especificar o período máximo de validade em meses que deseja. Selecione **auditoria** ou **negação** para o efeito da apólice seguindo as orientações nas secções acima. Em seguida, selecione o botão de revisão + criar. 

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Ver Resultados de Conformidade

1. Volte para a lâmina 'Política' e selecione o separador de conformidade. Clique na atribuição de política para a visão dos resultados de conformidade.

    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img8.png)

1. A partir desta página pode filtrar os resultados através de cofres compatíveis ou não conformes. Aqui pode ver uma lista de cofres-chave não conformes no âmbito da atribuição de políticas. Um cofre é considerado incompatível se algum dos componentes (certificados) do cofre não for conforme. Pode selecionar um cofre individual para visualizar os componentes individuais não conformes (certificados). 


    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img9.png)

1. Ver o nome dos componentes dentro de um cofre que não são compatíveis


    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img10.png)

1. Se precisar de verificar se os utilizadores estão a ser negados a capacidade de criar recursos dentro do cofre-chave, pode clicar no separador **Eventos componentes (pré-visualização)** para ver um resumo das operações de certificado negadas com o solicitador e os prazos de pedidos. 


    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img11.png)

## <a name="feature-limitations"></a>Limitações de recursos

A atribuição de uma política com efeito de "negação" pode levar até 30 minutos (caso médio) e 1 hora (pior caso) para começar a negar a criação de recursos não conformes. A avaliação política dos componentes existentes num cofre pode demorar até 1 hora (caso médio) e 2 horas (pior caso) antes de os resultados de conformidade serem visualizados no portal UI. Se os resultados da conformidade aparecerem como "Não Iniciados" pode ser devido às seguintes razões:
- A avaliação política ainda não está concluída. A latência inicial da avaliação pode demorar até 2 horas no pior dos cenários. 
- Não há cofres chave no âmbito da atribuição de apólices.
- Não há cofres-chave com certificados no âmbito da atribuição de apólices.

> [!NOTE]
> Os modos Azure Policy [Resouce Provider](../../governance/policy/concepts/definition-structure.md#resource-provider-modes), como os do Azure Key Vault, fornecem informações sobre a conformidade na página [de Conformidade dos Componentes.](../../governance/policy/how-to/get-compliance-data.md#component-compliance)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [serviço Azure Policy](../../governance/policy/overview.md)
- Ver amostras do [Cofre-Chave: Definições de política incorporadas do Cofre-Chave](../../governance/policy/samples/built-in-policies.md#key-vault)