---
title: Integrar o Azure Key Vault no Azure Policy
description: Saiba como integrar o Cofre chave Azure com a política azure
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 6b54dc27f8a3e88dedb0552b1ac7fb675d75121a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424595"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrar o Azure Key Vault no Azure Policy

[A Azure Policy](../../governance/policy/index.yml) é uma ferramenta de governação que dá aos utilizadores a capacidade de auditar e gerir o seu ambiente Azure em escala. A Política Azure oferece a capacidade de colocar guarda-costas nos recursos do Azure para garantir que estão em conformidade com as regras políticas atribuídas. Permite que os utilizadores realizem auditorias, aplicação em tempo real e remediação do seu ambiente Azure. Os resultados das auditorias realizadas por política estarão disponíveis para os utilizadores num dashboard de conformidade onde poderão ver uma perfuração dos recursos e componentes que estão em conformidade e que não são.  Para mais informações, consulte a [visão geral do serviço De Política Azure.](../../governance/policy/overview.md)

Exemplo Cenários de utilização:

- Pretende melhorar a postura de segurança da sua empresa implementando requisitos em torno de tamanhos mínimos de chave e períodos máximos de validade de certificados nos cofres-chave da sua empresa, mas não sabe quais as equipas que serão conformes e que não são. 
- Atualmente não tem uma solução para realizar uma auditoria em toda a sua organização, ou está a realizar auditorias manuais do seu ambiente, pedindo às equipas individuais dentro da sua organização que informem a sua conformidade. Está à procura de uma forma de automatizar esta tarefa, realizar auditorias em tempo real e garantir a exatidão da auditoria.
- Você quer impor as políticas de segurança da sua empresa e impedir que os indivíduos criem certificados auto-assinados, mas você não tem uma maneira automatizada de bloquear a sua criação. 
- Você quer relaxar alguns requisitos para as suas equipas de teste, mas você quer manter controlos apertados sobre o seu ambiente de produção. Precisa de uma forma automatizada simples de separar a aplicação dos seus recursos. 
- Você deve ter certeza de que você pode reverter a aplicação de novas políticas em caso de um problema no site ao vivo. Precisa de uma solução de um clique para desligar a aplicação da política. 
- Está a contar com uma solução de terceiros para auditar o seu ambiente e pretende utilizar uma oferta interna da Microsoft. 

## <a name="types-of-policy-effects-and-guidance"></a>Tipos de efeitos políticos e orientação

**Auditoria**: Quando o efeito de uma política for definida para ser auditado, a política não causará alterações no seu ambiente. Apenas o alertará para componentes como certificados que não cumpram as definições de política dentro de um âmbito especificado, marcando estes componentes como não conformes no painel de controlo da política. A auditoria é padrão se não for selecionado nenhum efeito político. 

**Negar**: Quando o efeito de uma política for definido para negar, a política bloqueará a criação de novos componentes, como certificados, bem como bloqueará novas versões dos componentes existentes que não cumpram a definição de política. Os recursos não conformes existentes dentro de um cofre chave não são afetados. As capacidades de "auditoria" continuarão a funcionar.

## <a name="available-built-in-policy-definitions"></a>Definições políticas "incorporadas" disponíveis

Key Vault criou um conjunto de políticas, que você pode atribuir para cenários comuns para gerir certificados. Estas políticas são 'Embutida', o que significa que não exigem que escreva qualquer JSON personalizado para as permitir e estão disponíveis no portal Azure para que você atribua. Ainda pode personalizar certos parâmetros para se adaptar às necessidades da sua organização. 

As oito políticas de pré-visualização são as seguintes.

### <a name="manage-certificate-validity-period-preview"></a>Gerir o período de validade do certificado (pré-visualização)

Esta política permite-lhe gerir o período máximo de validade dos seus certificados armazenados no cofre chave. É uma boa prática de segurança limitar o período máximo de validade dos seus certificados. Se uma chave privada do seu certificado ficar comprometida sem ser detetada, a utilização de certificados de curta duração minimiza o prazo para danos contínuos e reduz o valor do certificado a um intruso. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Gerir os tipos de chaves de certificado permitidos (pré-visualização)
Esta política permite-lhe restringir o tipo de certificados que podem estar no seu cofre chave. Pode utilizar esta política para se certificar de que as chaves privadas do certificado são RSA, ECC ou são apoiadas por HSM. Pode escolher entre a seguinte lista quais os tipos de certificados permitidos.
- RSA
- RSA - HSM
- ECC 
- ECC - HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Gerir os gatilhos de ação vitalícia do certificado (pré-visualização)

Esta política permite-lhe gerir a ação vitalícia especificada para certificados que estão dentro de um determinado número de dias após a sua expiração ou atingiram uma certa percentagem da sua vida útil. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Gerir os certificados emitidos por um CA integrado (pré-visualização)

Se utilizar uma autoridade de certificados integrado key Vault (Digicert ou GlobalSign) e pretender que os utilizadores utilizem um ou qualquer um destes fornecedores, pode utilizar esta política para auditar ou fazer cumprir a sua seleção. Esta política também pode ser utilizada para auditar ou negar a criação de certificados auto-assinados no cofre-chave. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Gerir os certificados emitidos por um CA integrado (pré-visualização)

Se utilizar uma autoridade de certificados internos ou uma autoridade de certificados não integrada com o cofre chave e pretender que os utilizadores utilizem uma autoridade de certificado a partir de uma lista que fornece, pode utilizar esta política para criar uma lista permitida de autoridades de certificados pelo nome do emitente. Esta política também pode ser utilizada para auditar ou negar a criação de certificados auto-assinados no cofre-chave. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Gerir nomes de curvas permitidos para certificados de criptografia de curva elíptica (pré-visualização)
Se utilizar criptografia de curva elíptica ou certificados ECC, pode personalizar uma lista permitida de nomes curvos da lista abaixo. A opção predefinida permite todos os seguintes nomes de curvas. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Gerir o tamanho mínimo da chave para certificados RSA (pré-visualização)
Se utilizar certificados RSA, pode escolher um tamanho mínimo de chave que os seus certificados devem ter. Pode selecionar uma opção na lista abaixo. 
- Bit 2048
- 3072 bit
- 4096 bit

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Gerir certificados que se encontram dentro de um número determinado de dias de expiração (pré-visualização)
O seu serviço pode sofrer uma paragem se um certificado que não está a ser monitorizado adequadamente não for rodado antes da sua expiração. Esta política é fundamental para garantir que os seus certificados armazenados no cofre das chaves estão a ser monitorizados. Recomenda-se que aplique esta política várias vezes com diferentes limiares de validade, por exemplo, em 180, 90, 60 e 30 dias. Esta política pode ser usada para monitorizar e caducar o certificado de triagem na sua organização. 

## <a name="example-scenario"></a>Cenário de Exemplo

Gere-se um cofre de chaves usado por várias equipas que contém 100 certificados, e quer ter a certeza de que nenhum dos certificados no cofre de chaves é válido por mais de 2 anos.

1. Atribui a política de prazo de validade do [certificado De gestão,](#manage-certificate-validity-period-preview) especifica que o prazo máximo de validade de um certificado é de 24 meses e define o efeito da apólice para "auditoria". 
1. Vê o relatório de [conformidade no portal Azure](#view-compliance-results)e descobre que 20 certificados não cumprem e são válidos por > 2 anos, e os restantes certificados estão em conformidade. 
1. Contacte os proprietários destes certificados e comunique o novo requisito de segurança de que os certificados não podem ser válidos por mais de 2 anos. Algumas equipas respondem e 15 dos certificados foram renovados com um prazo máximo de validade de 2 anos ou menos. Outras equipas não respondem, e ainda tens 5 certificados não conformes no teu cofre chave.
1. Muda-se o efeito da política que atribuiu para "negar". Os 5 certificados não conformes não são revogados e continuam a funcionar. No entanto, não podem ser renovados com um período de validade superior a 2 anos. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Habilitar e gerir uma política de cofre chave através do portal Azure

### <a name="select-a-policy-definition"></a>Selecione uma Definição de Política

1. Inicie sessão no portal do Azure. 
1. Pesquise "Política" na Barra de Pesquisa e Selecione **A Política**.

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img1.png)

1. Na janela Política, selecione **Definições**.

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img2.png)

1. No filtro categoria, **selecione tudo** e selecione **Key Vault**. 

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img3.png)

1. Agora você deve ser capaz de ver todas as políticas disponíveis para visualização pública, para Azure Key Vault. Certifique-se de que leu e compreendeu a secção de orientação política acima e selecione uma política que pretende atribuir a um âmbito.  

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Atribuir uma Política a um Âmbito 

1. Selecione uma política que pretende aplicar, neste exemplo, a política do Período de Validade do **Certificado de Gestão** é mostrada. Clique no botão de atribuição no canto superior esquerdo.

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img5.png)
  
1. Selecione a subscrição onde pretende que a política seja aplicada. Pode optar por restringir o âmbito a apenas um grupo de recursos únicos dentro de uma subscrição. Se quiser aplicar a apólice a toda a subscrição e excluir alguns grupos de recursos, também pode configurar uma lista de exclusão. Desloque o seletor de aplicação da política para **ativado** se pretender que o efeito da política (auditoria ou negação) ocorra ou **desativado** para desligar o efeito (auditoria ou negar). 

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img6.png)

1. Clique no separador de parâmetros na parte superior do ecrã para especificar o período máximo de validade nos meses que desejar. Selecione **auditoria** ou **negue** o efeito da apólice seguindo as orientações nas secções acima referidas. Em seguida, selecione a revisão + criar o botão. 

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Ver Resultados de Conformidade

1. Volte à lâmina política e selecione o separador de conformidade. Clique na atribuição de política para a sua desejaver.

    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img8.png)

1. A partir desta página pode filtrar os resultados através de cofres conformes ou não conformes. Aqui pode ver uma lista de cofres-chave não conformes no âmbito da atribuição de políticas. Um cofre é considerado incompatível se algum dos componentes (certificados) no cofre não for compatível. Pode selecionar um cofre individual para visualizar os componentes não conformes individuais (certificados). 


    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img9.png)

1. Veja o nome dos componentes dentro de um cofre que não são compatíveis


    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img10.png)

1. Se precisar de verificar se os utilizadores estão a ser negados a capacidade de criar recursos dentro do cofre de chaves, pode clicar no separador **Component Events (pré-visualização)** para ver um resumo das operações de certificados negados com o solicitador e os carimbos de tempo dos pedidos. 


    ![Visão geral de como funciona o Cofre de Chaves Azure](../media/policy-img11.png)

## <a name="feature-limitations"></a>Limitações de funcionalidades

A atribuição de uma política com um efeito "negar" pode demorar até 30 minutos (caso médio) e 1 hora (na pior das hipóteses) para começar a negar a criação de recursos não conformes. A avaliação política dos componentes existentes num cofre pode demorar até 1 hora (caso médio) e 2 horas (na pior das hipóteses) antes que os resultados da conformidade sejam visualizados no portal UI. Se os resultados da conformidade aparecerem como "Não Iniciados" pode dever-se às seguintes razões:
- A avaliação política ainda não está concluída. A latência inicial de avaliação pode demorar até 2 horas no pior cenário. 
- Não há cofres chave no âmbito da missão política.
- Não há cofres-chave com certificados no âmbito da atribuição de apólices. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [serviço de Política Azure](../../governance/policy/overview.md)
- Ver amostras do Cofre chave: [Chave Vault embutida definições políticas](../../governance/policy/samples/built-in-policies.md#key-vault)