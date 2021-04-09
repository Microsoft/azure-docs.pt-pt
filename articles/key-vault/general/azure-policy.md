---
title: Integrar o Azure Key Vault no Azure Policy
description: Saiba como integrar o Azure Key Vault com a política Azure
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 10/15/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6ac4d0e0744bfc82a686671234e013b2dd717146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927758"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrar o Azure Key Vault no Azure Policy

[A Azure Policy](../../governance/policy/index.yml) é uma ferramenta de governação que dá aos utilizadores a capacidade de auditar e gerir o seu ambiente Azure em escala. A Azure Policy fornece a capacidade de colocar guarda-costas nos recursos da Azure para garantir que estão em conformidade com as regras políticas atribuídas. Permite que os utilizadores realizem auditorias, execução em tempo real e reparação do seu ambiente Azure. Os resultados das auditorias realizadas por política estarão disponíveis para os utilizadores num painel de conformidade onde poderão ver um furo de que recursos e componentes estão em conformidade e que não estão.  Para mais informações, consulte a [visão geral do serviço Azure Policy](../../governance/policy/overview.md).

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

Key Vault criou um conjunto de políticas, que podem ser usadas para gerir chaves, certificados e objetos secretos. Estas políticas são 'Built-In', o que significa que não exigem que escreva qualquer JSON personalizado para as habilitar e estão disponíveis no portal Azure para que possa atribuir. Você ainda pode personalizar certos parâmetros para se adequar às necessidades da sua organização.

# <a name="certificate-policies"></a>[Políticas do Certificado](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Os certificados devem ter o período máximo de validade especificado (pré-visualização)

Esta política permite-lhe gerir o período máximo de validade dos seus certificados armazenados no cofre de chaves. É uma boa prática de segurança limitar o período máximo de validade dos seus certificados. Se uma chave privada do seu certificado ficar comprometida sem ser detetada, a utilização de certificados de curta duração minimiza o prazo para danos em curso e reduz o valor do certificado a um intruso.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Os certificados devem utilizar tipos-chave permitidos (pré-visualização)

Esta política permite-lhe restringir o tipo de certificados que podem estar no seu cofre chave. Pode utilizar esta política para se certificar de que as chaves privadas do certificado são RSA, ECC ou são apoiadas pelo HSM. Pode escolher entre a seguinte lista quais os tipos de certificados permitidos.

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Os certificados devem ter os gatilhos de ação vitalício especificados (pré-visualização)

Esta política permite-lhe gerir a ação vitalícia especificada para certificados que se encontram dentro de um determinado número de dias após a sua expiração ou que atingiram uma certa percentagem da sua vida útil.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Os certificados devem ser emitidos pela autoridade de certificados integrados especificado (pré-visualização)

Se utilizar uma autoridade de certificados integrados Key Vault (Digicert ou GlobalSign) e pretender que os utilizadores utilizem um ou qualquer um destes fornecedores, pode utilizar esta política para auditar ou fazer cumprir a sua seleção. Esta política também pode ser utilizada para auditar ou negar a criação de certificados auto-assinados no cofre-chave.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Os certificados devem ser emitidos pela autoridade de certificados não integrada especificada (pré-visualização)

Se utilizar uma autoridade de certificados interno ou uma autoridade de certificados não integrada com cofre-chave e pretender que os utilizadores utilizem uma autoridade de certificados a partir de uma lista que forneça, pode utilizar esta política para criar uma lista permitida de autoridades de certificados por nome emitente. Esta política também pode ser utilizada para auditar ou negar a criação de certificados auto-assinados no cofre-chave.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Os certificados que utilizam a criptografia da curva elíptica devem ter permitido nomes de curvas (pré-visualização)

Se utilizar a criptografia da curva elíptica ou os certificados ECC, pode personalizar uma lista permitida de nomes de curvas da lista abaixo. A opção predefinição permite todos os seguintes nomes de curvas.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Certificados que utilizam criptografia RSA Gerencie o tamanho mínimo da chave para certificados RSA (pré-visualização)

Se utilizar certificados RSA, pode escolher um tamanho mínimo de chave que os seus certificados devem ter. Pode selecionar uma opção na lista abaixo.

- Bit de 2048
- 3072 bit
- 4096 bit

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Gerir certificados que se encontram dentro de um número especificado de dias de expiração (pré-visualização)

O seu serviço pode experimentar uma paragem se um certificado que não está a ser monitorizado adequadamente não for rodado antes da sua expiração. Esta política é fundamental para garantir que os seus certificados armazenados no cofre chave estão a ser monitorizados. Recomenda-se que aplique esta política várias vezes com diferentes limiares de validade, por exemplo, a limiares de 180, 90, 60 e 30 dias. Esta política pode ser usada para monitorizar e triagem expiração de certificado na sua organização.

# <a name="key-policies"></a>[Políticas-chave](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>As chaves não devem estar ativas por mais tempo do que o número especificado de dias (pré-visualização)

Se quiser certificar-se de que as suas chaves não estão ativas há mais de um número especificado de dias, pode utilizar esta política para verificar quanto tempo a sua chave está ativa.

**Se a sua chave tiver uma data de ativação definida,** esta política calculará o número de dias decorridos desde a data de **ativação** da chave até à data atual. Se o número de dias exceder o limiar definido, a chave será marcada como incompatível com a política.

**Se a sua chave não tiver uma data de ativação definida,** esta política calculará o número de dias decorridos desde a data de **criação** da chave até à data atual. Se o número de dias exceder o limiar definido, a chave será marcada como incompatível com a política.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>As chaves devem ser o tipo criptográfico especificado RSA ou CE (pré-visualização)

Esta política permite-lhe restringir o tipo de chaves que podem estar no cofre da chave. Pode utilizar esta política para se certificar de que as suas chaves são RSA, ECC ou são apoiadas pelo HSM. Pode escolher entre a seguinte lista quais os tipos de certificados permitidos.

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>As teclas que utilizam a criptografia da curva elíptica devem ter os nomes de curva especificados (pré-visualização)

Se utilizar criptografia de curva elíptica ou teclas ECC, pode personalizar uma lista permitida de nomes de curvas da lista abaixo. A opção predefinição permite todos os seguintes nomes de curvas.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>As teclas devem ter datas de validade definidas (pré-visualização)

Esta política audita todas as chaves dos cofres e chaves de bandeiras que não têm uma data de validade definida como incompatível. Também pode utilizar esta política para bloquear a criação de chaves que não tenham uma data de validade definida.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>As chaves devem ter mais do que o número especificado de dias antes da expiração (pré-visualização)

Se uma chave estiver demasiado perto da expiração, um atraso organizacional para rodar a chave pode resultar numa paragem. As teclas devem ser rodadas num determinado número de dias antes da expiração para proporcionar tempo suficiente para reagir a uma falha. Esta política irá auditar chaves que estão demasiado próximas da sua data de validade e permite definir este limiar em dias. Também pode utilizar esta política para evitar a criação de novas chaves que estejam demasiado próximas da sua data de validade.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>As chaves devem ser apoiadas por um módulo de segurança de hardware (pré-visualização)

Um HSM é um módulo de segurança de hardware que armazena chaves. Um HSM fornece uma camada física de proteção para chaves criptográficas. A chave criptográfica não pode deixar um HSM físico que proporciona um maior nível de segurança do que uma chave de software. Algumas organizações têm requisitos de conformidade que determinam a utilização de chaves HSM. Utilize esta política para auditar quaisquer chaves armazenadas no cofre que não seja apoiada pelo HSM. Também pode utilizar esta política para bloquear a criação de novas chaves que não sejam apoiadas pelo HSM. Esta política aplicar-se-á a todos os tipos-chave, RSA e ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>As teclas que utilizam a criptografia RSA devem ter um tamanho mínimo especificado de chave (pré-visualização)

A utilização de teclas RSA com tamanhos de teclas mais pequenos não é uma prática de design segura. Pode estar sujeito a normas de auditoria e certificação que determinam a utilização de um tamanho mínimo de chave. A seguinte política permite-lhe definir um requisito mínimo de tamanho de chave no seu cofre de chaves. Pode auditar chaves que não satisfaçam este requisito mínimo. Esta política também pode ser utilizada para bloquear a criação de novas chaves que não satisfaçam o requisito mínimo de tamanho das chaves.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>As chaves devem ter o período máximo de validade especificado (pré-visualização)

Gerencie os seus requisitos de conformidade organizacional especificando o tempo máximo em dias para que uma chave possa ser válida dentro do seu cofre de chaves. As teclas válidas por mais tempo do que o limiar definido serão marcadas como incompatíveis. Também pode utilizar esta política para bloquear a criação de novas teclas que tenham uma data de validade definida por mais tempo do que o período máximo de validade especificado.

# <a name="secret-policies"></a>[Políticas Secretas](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Os segredos não devem estar ativos por mais tempo do que o número especificado de dias (pré-visualização)

Se quiser certificar-se de que os seus segredos não estão ativos há mais de um número especificado de dias, pode usar esta política para auditar quanto tempo o seu segredo está ativo.

**Se o seu segredo tiver uma data de ativação definida,** esta política calculará o número de dias decorridos desde a data de **ativação** do segredo até à data atual. Se o número de dias exceder o limiar definido, o segredo será marcado como incompatível com a política.

**Se o seu segredo não tiver uma data de ativação definida,** esta política calculará o número de dias que decorreram desde a data de **criação** do segredo até à data atual. Se o número de dias exceder o limiar definido, o segredo será marcado como incompatível com a política.

### <a name="secrets-should-have-content-type-set-preview"></a>Os segredos devem ter conjunto de tipo de conteúdo (pré-visualização)

Qualquer texto simples ou ficheiro codificado pode ser armazenado como um segredo de cofre chave. No entanto, a sua organização pode querer definir diferentes políticas de rotação e restrições em senhas, cadeias de conexão ou certificados armazenados como chaves. Uma etiqueta de tipo de conteúdo pode ajudar um utilizador a ver o que é armazenado num objeto secreto sem ler o valor do segredo. Você pode usar esta política para auditar segredos que não têm um conjunto de etiquetas de tipo de conteúdo. Também pode usar esta política para evitar que novos segredos sejam criados se não tiverem um conjunto de etiquetas de tipo de conteúdo.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Segredos devem ter a data de validade definida (pré-visualização)

Esta política audita todos os segredos no seu cofre chave e sinaliza segredos que não têm uma data de validade definida como incompatível. Também pode usar esta política para bloquear a criação de segredos que não têm uma data de validade definida.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Os segredos devem ter mais do que o número especificado de dias antes da expiração (pré-visualização)

Se um segredo estiver muito perto da expiração, um atraso organizacional para rodar o segredo pode resultar em uma paragem. Os segredos devem ser rodados num número especificado de dias antes da expiração para fornecer tempo suficiente para reagir a uma falha. Esta política irá auditar segredos que estão muito perto da data de validade e permite definir este limiar em dias. Também pode usar esta política para evitar a criação de novos segredos que estão demasiado próximos da sua data de validade.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Os segredos devem ter o período máximo de validade especificado (pré-visualização)

Gerencie os seus requisitos de conformidade organizacional especificando o tempo máximo em dias para que um segredo possa ser válido dentro do seu cofre de chaves. Os segredos que são válidos por mais tempo do que o limiar definido serão marcados como incompatíveis. Também pode utilizar esta política para bloquear a criação de novos segredos que tenham uma data de validade definida por mais tempo do que o período máximo de validade especificado.

---

## <a name="example-scenario"></a>Cenário de Exemplo

Gere um cofre usado por várias equipas que contém 100 certificados, e quer ter a certeza de que nenhum dos certificados no cofre da chave é válido por mais de 2 anos.

1. A atribuição dos Certificados deverá ter a política **de prazo de validade máxima especificada,** especificar que o prazo máximo de validade de um certificado é de 24 meses e definir o efeito da apólice para "auditoria". 
1. Veja o [relatório de conformidade no portal Azure](#view-compliance-results)e descubra que 20 certificados são incompatíveis e válidos por > 2 anos, e os restantes certificados são conformes. 
1. Contacte os proprietários destes certificados e comunique o novo requisito de segurança de que os certificados não podem ser válidos por mais de 2 anos. Algumas equipas respondem e 15 dos certificados foram renovados com um período máximo de validade de 2 anos ou menos. Outras equipas não respondem, e ainda tens 5 certificados não conformes no cofre.
1. Muda-se o efeito da política que atribuiu a "negar". Os 5 certificados não conformes não são revogados e continuam a funcionar. No entanto, não podem ser renovados com um período de validade superior a 2 anos. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Habilitar e gerir uma política de Cofre-Chave através do portal Azure

### <a name="select-a-policy-definition"></a>Selecione uma definição de política

1. Inicie sessão no portal do Azure. 
1. Procure "Política" na Barra de Pesquisa e Selecione.

    ![Screenshot que mostra a Barra de Pesquisa.](../media/policy-img1.png)

1. Na janela Política, selecione **Definições**.

    ![Screenshot que realça a opção Definições.](../media/policy-img2.png)

1. No filtro de categoria, **desescolh-se de tudo** e selecione o Cofre de **Chaves**. 

    ![Screenshot que mostra o Filtro de Categoria e a categoria Key Vault selecionada.](../media/policy-img3.png)

1. Agora você deve ser capaz de ver todas as políticas disponíveis para visualização pública, para Azure Key Vault. Certifique-se de que leu e compreendeu a secção de orientação de política acima e selecione uma política que pretende atribuir a um âmbito.  

    ![Screenshot que mostra as políticas que estão disponíveis para visualização pública.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Atribuir uma Política a um Âmbito 

1. Selecione uma política que pretende aplicar, neste exemplo, é apresentada a política do Período de Validade do **Certificado de Gestão.** Clique no botão de atribuição no canto superior esquerdo.

    ![Screenshot que mostra a política do Período de Validade do Certificado de Gestão.](../media/policy-img5.png)
  
1. Selecione a subscrição onde deseja que a apólice seja aplicada. Pode optar por restringir o âmbito a apenas um grupo de recursos dentro de uma subscrição. Se pretender aplicar a política a toda a subscrição e excluir alguns grupos de recursos, também pode configurar uma lista de exclusão. Desacorra o seletor de execução da política para **Ativar** se pretender que o efeito da política (auditoria ou negação) ocorra ou **desative** o efeito (auditoria ou negação). 

    ![Screenshot que mostra onde pode optar por restringir o âmbito a apenas um grupo de recursos dentro de uma subscrição.](../media/policy-img6.png)

1. Clique no separador parâmetros na parte superior do ecrã para especificar o período máximo de validade em meses que deseja. Selecione **auditoria** ou **negação** para o efeito da apólice seguindo as orientações nas secções acima. Em seguida, selecione o botão de revisão + criar. 

    ![Screenshot que mostra o separador Parâmetros onde pode especificar o período máximo de validade em meses que deseja.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Ver Resultados de Conformidade

1. Volte para a lâmina 'Política' e selecione o separador de conformidade. Clique na atribuição de política para a visão dos resultados de conformidade.

    ![Screenshot que mostra o separador Conformidade onde pode selecionar a atribuição de política para a qual deseja visualizar os resultados de conformidade.](../media/policy-img8.png)

1. A partir desta página pode filtrar os resultados através de cofres compatíveis ou não conformes. Aqui pode ver uma lista de cofres-chave não conformes no âmbito da atribuição de políticas. Um cofre é considerado incompatível se algum dos componentes (certificados) do cofre não for conforme. Pode selecionar um cofre individual para visualizar os componentes individuais não conformes (certificados). 


    ![Screenshot que mostra uma lista de cofres-chave não conformes no âmbito da atribuição de apólices.](../media/policy-img9.png)

1. Ver o nome dos componentes dentro de um cofre que não são compatíveis


    ![Screenshot que mostra onde você pode ver o nome dos componentes dentro de um cofre que não são compatíveis.](../media/policy-img10.png)

1. Se precisar de verificar se os utilizadores estão a ser negados a capacidade de criar recursos dentro do cofre-chave, pode clicar no separador **Eventos componentes (pré-visualização)** para ver um resumo das operações de certificado negadas com o solicitador e os prazos de pedidos. 


    ![Visão geral de como funciona o Azure Key Vault](../media/policy-img11.png)

## <a name="feature-limitations"></a>Limitações da Funcionalidade

A atribuição de uma política com efeito de "negação" pode levar até 30 minutos (caso médio) e 1 hora (pior caso) para começar a negar a criação de recursos não conformes. A avaliação política dos componentes existentes num cofre pode demorar até 1 hora (caso médio) e 2 horas (pior caso) antes de os resultados de conformidade serem visualizados no portal UI. Se os resultados da conformidade aparecerem como "Não Iniciados" pode ser devido às seguintes razões:
- A avaliação política ainda não está concluída. A latência inicial da avaliação pode demorar até 2 horas no pior dos cenários. 
- Não há cofres chave no âmbito da atribuição de apólices.
- Não há cofres-chave com certificados no âmbito da atribuição de apólices.

> [!NOTE]
> Os modos Azure Policy [Resouce Provider](../../governance/policy/concepts/definition-structure.md#resource-provider-modes), como os do Azure Key Vault, fornecem informações sobre a conformidade na página [de Conformidade dos Componentes.](../../governance/policy/how-to/get-compliance-data.md#component-compliance)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [serviço Azure Policy](../../governance/policy/overview.md)
- Ver amostras do [Cofre-Chave: Definições de política incorporadas do Cofre-Chave](../../governance/policy/samples/built-in-policies.md#key-vault)
