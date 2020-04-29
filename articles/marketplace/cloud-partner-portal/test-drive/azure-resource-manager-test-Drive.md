---
title: Unidade de Teste do Gestor de Recursos Azure [ Unidade de Teste ] Mercado Azure
description: Construa um Marketplace Test Drive utilizando o Gestor de Recursos Azure
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275939"
---
# <a name="azure-resource-manager-test-drive"></a>Versão de Teste do Azure Resource Manager

Este artigo é para editores que têm a sua oferta no Azure Marketplace, ou que estão no AppSource, mas querem construir o seu Test Drive apenas com recursos Azure.

Um modelo de Gestor de Recursos Azure (Gestor de Recursos) é um recipiente codificado de recursos Azure que projeta para melhor representar a sua solução. Se não está familiarizado com o que é um modelo de Gestor de Recursos, leia sobre [compreender os modelos do Gestor](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) de Recursos e autorizar [modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) de Gestor de Recursos para se certificar de que sabe construir e testar os seus próprios modelos.

O que o Test Drive faz é que ele leva o modelo fornecido do Gestor de Recursos e faz uma implementação de todos os recursos necessários desse modelo de Gestor de Recursos para um grupo de recursos.

Se optar por construir um Test Drive do Gestor de Recursos Azure, os requisitos são para:

- Construa, teste e, em seguida, carregue o seu modelo de Gestor de Recursos test drive.
- Configure todos os metadados e configurações necessários para ativar a sua Unidade de Teste.
- Republique a sua oferta com test drive ativado.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Como construir um Test Drive de Gestor de Recursos Azure

Aqui está o processo para a construção de um Test Drive Do Gestor de Recursos Azure:

1. Desenhe o que quer que os seus clientes façam num diagrama de fluxo.
1. Defina as experiências que gostaria que os seus clientes construíssem.
1. Com base nas definições acima, decida quais as peças e recursos necessários para que os clientes realizem tal experiência: por exemplo, a instância D365, ou um website com uma base de dados.
1. Construa o design localmente, e teste a experiência.
1. Embalem a experiência numa implantação do modelo ARM e a partir daí:
    1. Definir que partes dos recursos são parâmetros de entrada;
    1. Quais são as variáveis;
    1. Que saídas são dadas à experiência do cliente.
1. Publique, teste e vá ao vivo.

A parte mais importante da construção de um Test Drive do Gestor de Recursos Azure é definir que cenário s quer que os seus clientes experimentem. És um produto de firewall e queres despromover o quão bem lidas com os ataques de injeção de guião? É um produto de armazenamento e quer despromover a rapidez e facilidade comque a sua solução comprime ficheiros?

Certifique-se de que passa um tempo suficiente a avaliar quais são as melhores formas de mostrar o seu produto. Especificamente em torno de todos os recursos necessários, uma vez que torna a embalagem do modelo do Gestor de Recursos suficientemente fácil.

Para continuar com o nosso exemplo de firewall, a arquitetura pode ser que você precisa de um URL IP público para o seu serviço e outro URL IP público para o site que a sua firewall está protegendo. Cada IP é implantado numa Máquina Virtual e ligado juntamente com uma interface de rede de segurança + rede.

Uma vez projetado o pacote de recursos desejado, agora vem a escrita e construção do modelo Test Drive Resource Manager.

## <a name="writing-test-drive-resource-manager-templates"></a>Modelos de gestor de recursos de teste de escrita

Test Drive executa implementações num modo totalmente automatizado, e por isso, os modelos test drive têm algumas restrições descritas abaixo.

### <a name="parameters"></a>Parâmetros

A maioria dos modelos tem um conjunto de parâmetros. Os parâmetros definem nomes de recursos, tamanhos de recursos (por exemplo, tipos de contas de armazenamento ou tamanhos de máquinas virtuais), nomes de utilizadores e palavras-passe, nomes DNS e assim por diante. Quando implementa soluções utilizando o portal Azure, pode povoar manualmente todos estes parâmetros, escolher nomes DNS disponíveis ou nomes de contas de armazenamento, e assim por diante.

![Lista de parâmetros num Gestor de Recursos Azure](./media/azure-resource-manager-test-drive/param1.png)

No entanto, o Test Drive funciona num modo totalmente automático, sem interação humana, pelo que apenas suporta um conjunto limitado de categorias de parâmetros. Se um parâmetro no modelo test drive resource manager não cair numa das categorias suportadas, deve **substituir este parâmetro por um valor variável ou constante.**

Pode utilizar qualquer nome válido para os seus parâmetros, test Drive reconhece a categoria de parâmetroutilizando o valor do tipo metadados. Deve **especificar metadados para cada parâmetro de modelo,** caso contrário o seu modelo não passará na validação:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

Também é importante notar que **todos os parâmetros são opcionais,** por isso, se não\'quiser usar nenhum, não\'precisa.

### <a name="accepted-parameter-metadata-types"></a>Tipos de metadados de parâmetros aceites

| Tipo de Metadados   | Tipo parâmetro  | Descrição     | Valor da Amostra    |
|---|---|---|---|
| **baseuri**     | string          | Base URI do seu pacote de implementação| \//\<https:\.. \>.blob.core.windows.net/.\<\.\> |
| **nome de utilizador**    | string          | Novo nome de utilizador aleatório.| administrador68876      |
| **palavra-passe**    | corda segura    | Nova senha aleatória | Lp!ACS\^2kh     |
| **id sessão**   | string          | ID de sessão de test drive exclusivo (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

Test Drive inicializa este parâmetro com um **Uri base** do seu pacote de implementação, para que possa usar este parâmetro para construir Uri de qualquer ficheiro incluído no seu pacote.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Dentro do seu modelo, pode utilizar este parâmetro para construir um Uri de qualquer ficheiro a partir do seu pacote de implementação Test Drive. O exemplo abaixo mostra como construir um Uri do modelo ligado:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>o nome de utilizador

Test Drive inicializa este parâmetro com um novo nome de utilizador aleatório:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Valor da amostra:

    admin68876

Pode utilizar nomes de utilizador aleatórios ou constantes para a sua solução.

#### <a name="password"></a>palavra-passe

Test Drive inicializa este parâmetro com uma nova senha aleatória:

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Valor da amostra:

    Lp!ACS^2kh

Pode utilizar senhas aleatórias ou constantes para a sua solução.

#### <a name="session-id"></a>ID da sessão

Test Drive inicialize este parâmetro com um ID exclusivo da sessão de teste de representação GUID:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Valor da amostra:

    b8c8693e-5673-449c-badd-257a405a6dee

Pode utilizar este parâmetro para identificar exclusivamente a sessão test drive, se necessário.

### <a name="unique-names"></a>Nomes únicos

Alguns recursos do Azure, como contas de armazenamento ou nomes DNS, requerem nomes globalmente únicos.

Isto significa que sempre que o Test Drive implementa o modelo de Gestor\' de Recursos, cria um novo grupo de recursos com um nome **único** para todos os seus recursos. Por isso, é necessário utilizar a função de [corda única](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) concatenada com os seus nomes variáveis em IDs do grupo de recursos para gerar valores únicos aleatórios:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Certifique-se de que concatena o seu\'parâmetro/cordas variáveis (contosovm)\'com\'uma saída de cadeia única (resourceGroup().id),\'porque isso garante a singularidade e fiabilidade de cada variável.

Por exemplo, a maioria dos nomes de recursos não pode começar com um dígito, mas a função de corda única pode devolver uma corda, que começa com um dígito. Assim, se usar uma saída de corda única crua, as suas implementações falharão. 

Pode encontrar informações adicionais sobre regras e restrições de nomeação de recursos [neste artigo.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="deployment-location"></a>Localização de implantação

Pode disponibilizar-lhe test drive em diferentes regiões do Azure. A ideia é permitir que um utilizador escolha a região mais próxima, para proporcionar a experiência do utilizador da besta.

Quando o Test Drive cria uma instância do Laboratório, cria sempre um grupo de recursos na região escolhido por um utilizador e executa o seu modelo de implementação neste contexto de grupo. Assim, o seu modelo deve escolher a localização de implantação do grupo de recursos:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

E, em seguida, usar este local para cada recurso para uma instância específica do Laboratório:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Tem de se certificar de que a sua subscrição está autorizada a implementar todos os recursos que pretende utilizar em cada uma das regiões que está a selecionar. Além disso, você precisa ter certeza de que as suas imagens de máquina virtual estão disponíveis em todas as regiões que você vai ativar, caso contrário o seu modelo de implementação não funcionará para algumas regiões.

### <a name="outputs"></a>Saídas

Normalmente com modelos de Gestor de Recursos, pode implementar sem produzir qualquer saída. Isto porque conhece todos os valores que utiliza para preencher parâmetros de modelo e pode sempre inspecionar manualmente as propriedades de qualquer recurso.

No entanto, para os\'modelos test drive resource manager, é importante voltar ao Test Drive todas as informações, que são necessárias para ter acesso ao laboratório (UrIs do Site, nomes de anfitriões da Máquina Virtual, nomes de utilizadores e palavras-passe). Certifique-se de que todos os seus nomes de saída são legíveis porque estas variáveis são apresentadas ao cliente.

Não existem restrições relacionadas com saídas de modelos. Lembre-se, test Drive converte todos os valores de saída em **cordas,** por isso, se enviar um objeto para a saída, um utilizador verá a corda JSON.

Exemplo:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limites de subscrição

Mais uma coisa que deve ter em conta são os limites de subscrição e de serviço. Por exemplo, se quiser implementar até dez máquinas virtuais de 4 núcleos, tem de se certificar de que a subscrição que utiliza para o seu Laboratório permite-lhe utilizar 40 núcleos.

Pode encontrar mais informações sobre os limites de subscrição e serviço do Azure [neste artigo.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Como várias Unidades de Teste podem ser tomadas \# ao mesmo tempo, verifique se a sua subscrição pode lidar com os núcleos multiplicados pelo número total de Test Drives simultâneos que podem ser tomadas.

### <a name="what-to-upload"></a>O que carregar

O modelo test Drive Resource Manager é carregado como um ficheiro zip, que pode incluir vários artefactos de implementação, mas precisa de ter um ficheiro chamado **modelo principal.json**. Este ficheiro é modelo de implementação do Gestor de Recursos Azure, e test Drive usa-o para instantaneamente um Laboratório.

Se tiver recursos adicionais para além deste ficheiro, pode remetê-lo como um recurso externo dentro do modelo, ou pode incluir o recurso no ficheiro zip.

Durante a certificação de publicação, o Test Drive desaperta o seu pacote de implementação e coloca o seu conteúdo num recipiente interno de bolhas Test Drive. A estrutura do recipiente reflete a estrutura do seu pacote de implantação:

| pacote.zip                       | Recipiente de bolha test drive         |
|---|---|
| modelo principal.json                | \//\<https:\... \>.blob.core.windows.net/\<\... \>/modelo principal.json  |
| modelos/solução.json           | \//\<https:\... \>.blob.core.windows.net/\<\... \>/modelos/solução.json |
| scripts/warmup.ps1                | \//\<https:\... \>.blob.core.windows.net/\<\... \>/scripts/warmup.ps1  |


Chamamos um Uri deste contentor de bolhas Base Uri. Cada revisão do seu Laboratório tem o seu próprio recipiente de bolhas, e, portanto, cada revisão do seu Laboratório tem a sua própria Base Uri. Test Drive pode passar um Uri base do seu pacote de implantação desapertado no seu modelo através de parâmetros de modelo.

## <a name="transforming-template-examples-for-test-drive"></a>Exemplos de modelo de transformação para test drive

O processo de transformar uma arquitetura de recursos num modelo de Gestor de Recursos test drive pode ser assustador. Para ajudar a facilitar este\'processo, fizemos exemplos sobre como melhor transformar os modelos de [implantação atuais aqui](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Agora que tem o test drive construído, esta secção percorre cada um dos campos necessários para que publique com sucesso o seu Test Drive.

![Habilitar o Test Drive na interface do utilizador](./media/azure-resource-manager-test-drive/howtopub1.png)

O primeiro e mais importante campo é alternar se deseja o Test Drive ativado para a sua oferta ou não. Quando selecionar **Sim,** o resto do formulário com todos os campos necessários são apresentados para que possa preencher. Quando selecionar **Não,** o formulário fica desativado e se voltar a publicar com o Test Drive desativado, o test Drive é removido da produção.

Nota: Se existirem unidades de teste ativamente utilizadas pelos utilizadores, esses Test Drives continuarão a funcionar até que a sua sessão expire.

### <a name="details"></a>Detalhes

A próxima secção a preencher são os detalhes sobre a sua oferta test drive.

![Informações detalhadas do Test Drive](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrição -** *Obrigatório* É aqui que escreve a descrição principal sobre o que está no seu Test Drive. O cliente virá aqui para ler quais os cenários que o seu Test Drive irá cobrir sobre o seu produto. 

**Manual do utilizador -** *Necessário* Este é o passo aprofundado da sua experiência test drive. O cliente abrirá isto e poderá passar exatamente pelo que pretende que eles façam durante o test drive. É importante que este conteúdo seja fácil de entender e acompanhar! (Deve ser um ficheiro .pdf)

**Test Drive Demo Video -** *Recomendado* Similar ao Manual do Utilizador, é melhor incluir um tutorial de vídeo da sua experiência test drive. O cliente irá assistir a isto antes ou durante o test drive e pode passar exatamente pelo que você quer que eles façam durante o seu Test Drive. É importante que este conteúdo seja fácil de entender e acompanhar!

- **Nome** - Título do seu Vídeo
- **Link** - Deve ser um URL incorporado do seu tubo ou vídeo. Exemplo sobre como obter o url incorporado está abaixo:
- **Miniatura** - Deve ser uma imagem de alta qualidade (533x324) pixels. Recomenda-se tirar uma imagem de alguma parte da sua experiência test drive aqui.

Abaixo está a forma como estes campos aparecem para o seu cliente durante a sua experiência test drive.

![Localização dos campos Test Drive na oferta do Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuração Técnica

A próxima secção a preencher é onde carrega o modelo de Gestor de Recursos test drive e define como as instâncias de Test Drive funcionam especificamente.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instâncias -** *Necessário* É aqui que configura quantas instâncias quer, em que região e a rapidez com que os seus clientes podem obter o Test Drive.

- **Instâncias** - As regiões Select é onde escolhe onde o seu modelo de Gestor de Recursos test drive é implantado. Recomenda-se apenas escolher uma região onde mais espera que os seus clientes estejam localizados.
- **Hot** - Número de instâncias test drive que já estão implantadas e aguardam acesso por região selecionada. Os clientes podem aceder instantaneamente a este Test Drives em vez de terem de esperar por uma implementação. A contrapartida é que estes casos estão sempre a decorrer na sua subscrição Azure, pelo que incorrerão num custo de uptime maior. É altamente recomendado ter **pelo menos uma instância Hot**, uma vez que a maioria dos seus clientes não quer esperar que as implementações completas terminem e, por isso, há uma queda no uso do cliente.
- **Warm** - Número de instâncias test drive por região que foram implantadas e, em seguida, o VM foi parado e armazenado no armazenamento Azure. O tempo de espera para casos quentes é mais lento do que os casos hot, mas o custo de armazenamento de tempo de espera também é mais barato.
- **Frio** - Número de instâncias de Test Drive por região que podem ser implantadas. As instâncias frias requerem que todo o modelo do Gestor de Recursos test drive passe por uma implementação no momento em que um cliente solicita a Test Drive, por isso é mais lento do que as instâncias Hot ou Warm. No entanto, a contrapartida é que só tem de pagar durante a duração do Test Drive.

Neste momento calcula o número total de potenciais Test Drives simultâneos que vai disponibilizar, e verificar se o seu limite de quota para a sua subscrição pode lidar com esse valor simultâneo:

**(Número de regiões selecionadas x instâncias quentes) + (Número de regiões selecionadas x instâncias quentes) + (Número de regiões selecionadas x instâncias frias)**

**Duração do test drive (horas) -** Duração *necessária* para \# quanto tempo o Test Drive permanecerá ativo, em horas. O Test Drive termina automaticamente após o fim deste período de tempo.

**Modelo** de Gestor de Recursos de Unidade de Teste - *Necessito* de carregar o seu modelo de Gestor de Recursos aqui. Este é o ficheiro que construiu na secção anterior acima. Nomeie o ficheiro de modelo principal: "main-template.json" e certifique-se de que o seu modelo de Gestor de Recursos contém parâmetros de saída para variáveis-chave que são necessárias. (Deve ser um ficheiro .zip)

**Informações de acesso -** *Necessárias* Depois de um cliente obter o seu Test Drive, a informação de acesso é-lhes apresentada. Estas instruções destinam-se a partilhar os parâmetros de saída úteis do seu modelo de Gestor de Recursos test drive. Para incluir parâmetros de saída, utilize suportes duplos encaracolados (por exemplo, **{{outputname}}**), e serão inseridos corretamente no local. (A formatação de cordas HTML é recomendada aqui para renderizar na extremidade frontal).

### <a name="test-drive-deployment-subscription-details"></a>Detalhes da subscrição de implementação de unidade de teste

A secção final a preencher é poder implementar automaticamente as Unidades de Teste ligando a sua Subscrição Azure e o Diretório Ativo Azure (AD).

![Detalhes da subscrição de implementação test Drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**Id de subscrição Azure -** *Isto exige* que este conceda acesso aos serviços Azure e ao portal Azure. A subscrição é onde o uso de recursos é reportado e os serviços são cobrados. Se ainda não tiver uma subscrição **Azure separada** apenas para Test Drives, vá em frente e faça uma. Pode encontrar Ids de subscrição azure iniciando sessão no portal Azure e navegando para as Subscrições no menu do lado esquerdo. (Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subscrições do Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID azure AD Tenant -** *Necessário* Se você tiver um ID de\> inquilino já disponível você pode encontrá-lo abaixo no ID de Diretório - Diretório.

![Propriedades de Diretório Ativo Azure](./media/azure-resource-manager-test-drive/subdetails3.png)

Caso contrário, crie um novo Inquilino no Diretório Ativo Azure.

![Lista de inquilinos do Diretório Ativo Azure](./media/azure-resource-manager-test-drive/subdetails4.png)

![Defina a organização, domínio e país/região para o inquilino da AD Azure](./media/azure-resource-manager-test-drive/subdetails5.png)

![Confirme a seleção](./media/azure-resource-manager-test-drive/subdetails6.png)

**Id da app Azure AD -** O próximo passo *exigido* é criar e registar uma nova aplicação. Utilizaremos esta aplicação para efetuar operações na sua instância test drive.

1. Navegue para o diretório recém-criado ou já existente e selecione o diretório Azure Ative no painel de filtros.
2. Pesquise "Registos de aplicações" e clique em "Adicionar"
3. Forneça um nome de candidatura.
4. Selecione o tipo de "Web app / API"
5. Fornecer qualquer valor em URL de\'inscrição, não usaremos esse campo.
6. Clique em criar.
7. Depois de criada a aplicação,\> vá ao Properties - Desloque a aplicação como multi-inquilino e atinja save.

Clique em Guardar. O último passo é pegar no ID da aplicação para esta aplicação registada e colá-lo no campo Test Drive aqui.

![Detalhe de ID da aplicação Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Dado que estamos a usar a aplicação para implementar a subscrição, precisamos adicionar a aplicação como contribuinte na subscrição. As instruções para estas são as seguintes:

1. Navegue na lâmina de Subscrições e selecione a subscrição apropriada que está a utilizar apenas para o Test Drive.
1. Clique em **Controlo de acesso (IAM)**.
1. Clique no separador de **atribuições de role.**  ![Adicione um novo diretor de Controlo de Acesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Clique em **Adicionar a atribuição de funções**.
1. Desinuque o papel de **Contribuinte.**
1. Digite em nome da aplicação Azure AD e selecione a aplicação para atribuir a função.
    ![Adicione as permissões](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Clique em **Guardar**.

**Chave de aplicação Azure AD -** *Obrigatório* O campo final é gerar uma chave de autenticação. Em baixo das teclas, adicione uma Descrição chave, detete a duração para nunca expirar e, em seguida, selecione guardar. É **importante** evitar ter uma chave caducada, que irá quebrar o seu test drive em produção. Copie este valor e cole-o no campo de Test Drive necessário.

![Mostra as Chaves para a aplicação Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem todos os campos de Test Drive preenchidos, vá e **republique** a sua oferta. Uma vez que o seu Test Drive tenha passado pela certificação, deverá testar extensivamente a experiência do cliente na **pré-visualização** da sua oferta. Inicie uma Unidade de Teste na UI e abra a subscrição Azure dentro do portal Azure e verifique se as suas Unidades de Teste estão a ser totalmente implementadas corretamente.

![Portal do Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

É importante notar que não elimina quaisquer instâncias de Test Drive, uma vez que são provisionadas para os seus clientes, pelo que o serviço Test Drive limpará automaticamente estes Grupos de Recursos depois de um cliente terminar com o mesmo.

Uma vez que se sinta confortável com a sua oferta de Pré-visualização, agora é hora de **ir ao vivo!** Existe um processo final de revisão da Microsoft uma vez que a oferta foi publicada para verificar o final da experiência final. Se por alguma razão a oferta for rejeitada, enviaremos uma notificação ao contacto de engenharia para a sua oferta explicando o que terá de ser corrigido.

Se tiver mais perguntas, estiver à procura de conselhos de resolução de problemas, ou queira tornar o seu Test Drive mais bem sucedido, por favor vá ao [FAQ, Troubleshooting, & Melhores Práticas.](./marketing-and-best-practices.md)
