---
title: Versão de teste de Gestor de recursos do Azure | Documentos da Microsoft
description: Criar uma unidade de teste do Marketplace com o Azure Resource Manager
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick .Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 615005e265f199cd6950123aa2af8b26f4fa4637
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456812"
---
# <a name="azure-resource-manager-test-drive"></a>Versão de teste de Gestor de recursos do Azure

Este artigo é para os publicadores que têm a oferta no Azure Marketplace ou que estão no AppSource, porém pretendem criar a unidade de teste com apenas os recursos do Azure.

Um modelo Azure Resource Manager (Resource Manager) é um contentor codificado de recursos do Azure que crie para representam melhor sua solução. Se não estiver familiarizado com que um modelo do Resource Manager é, ler sobre [Noções básicas sobre modelos do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) e [criar modelos do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) para se certificar de que já sabe como criar e testar seus próprios modelos.

O que faz o Test Drive é que ele usa o modelo do Resource Manager fornecidos e faz com que uma implementação de todos os recursos necessários a partir desse modelo do Resource Manager num grupo de recursos.

Se optar por criar uma unidade de teste do Azure Resource Manager, os requisitos são para:

- Criar, testar e, em seguida, carregue o modelo de Gestor de recursos de teste de unidade.
- Configure todos os metadados necessários e as definições para ativar sua versão de teste.
- Voltar a publicar a oferta com Test-Drive ativado.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Como criar uma unidade de teste do Azure Resource Manager

Este é o processo de criação de uma versão de teste de Gestor de recursos do Azure:

1. O que deseja que os clientes podem fazer num diagrama de fluxo de design.
1. Defina experiências de que pretende que os seus clientes para criar.
1. Com base nas definições de acima, decidir que partes e os recursos são necessários para os clientes realizar essa experiência: por exemplo, a instância de D365 ou um Web site com uma base de dados.
1. Criar o design localmente e testar a experiência.
1. Pacote de experiência numa implementação do modelo ARM e a partir daí:
    1. Definir que partes dos recursos são parâmetros de entrada;
    1. Quais são as variáveis;
    1. As saídas são fornecidas para a experiência do cliente.
1. Publicar, testar e aceda ao vivo.

É a parte mais importante sobre a criação de uma unidade de teste do Azure Resource Manager definir qual ou quais cenários de seus clientes para experimentar. É que um produto de firewall e quiser bem como lidar com ataques de injeção de script de demonstração? São a que um produto de armazenamento e pretender demonstrar como rápido e fácil de sua solução compacta arquivos?

Tornar surety gastar uma quantidade suficiente de tempo para avaliar quais são as melhores formas de exibir seu produto. Especificamente em torno de todos os recursos necessários seria necessário, já que isso faz empacotando o modelo do Resource Manager suficientemente mais fácil.

Para continuar com nosso exemplo de firewall, a arquitetura pode ser que precisa de um URL do IP público para o seu serviço e outra URL do IP público para o site no qual está a proteger o seu firewall. Cada IP é implementado numa máquina Virtual e ligada juntamente com um grupo de segurança de rede + a interface de rede.

Assim que tiver criado o pacote desejado de recursos, agora vem a escrita e a construção do modelo de teste da unidade do Resource Manager.

## <a name="writing-test-drive-resource-manager-templates"></a>Escrever o teste de unidade modelos do Resource Manager

Versão de teste é executado a implementações num modo totalmente automatizado e por isso, modelos de Test-Drive tem algumas restrições descritas abaixo.

### <a name="parameters"></a>Parâmetros

A maioria dos modelos têm um conjunto de parâmetros. Parâmetros de definem os nomes de recursos, tamanhos de recursos (por exemplo, tipos de contas de armazenamento ou tamanhos de máquinas virtuais), os nomes de utilizador e palavras-passe, nomes DNS e assim por diante. Quando implementa soluções através do portal do Azure, pode manualmente preencher todos esses parâmetros, escolher nomes DNS disponíveis ou nomes de conta de armazenamento e assim por diante.

![Lista de parâmetros num Gestor de recursos do Azure](./media/azure-resource-manager-test-drive/param1.png)

No entanto, versão de teste funciona num modo totalmente automático, sem interação humana, para que ela oferece suporte apenas um conjunto limitado de categorias de parâmetro. Se um parâmetro do modelo de Gestor de recursos de unidade de teste não se enquadra em uma das categorias suportadas, tem **substitua este parâmetro com um valor de variável ou constante.**

Pode utilizar qualquer nome válido para os parâmetros, a versão de teste reconhece a categoria de parâmetro com o valor de tipo de metadados. **Tem de especificar tipo de metadados para cada parâmetro de modelo**, caso contrário, o modelo não será aprovado na validação:

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

Também é importante ter em conta que **todos os parâmetros são opcionais**, por isso, se não\'t quiser utilizar algumas, não é\'precise.

### <a name="accepted-parameter-metadata-types"></a>Tipos de metadados de parâmetros aceites

| Tipo de metadados   | Tipo de parâmetro  | Descrição     | Valor de exemplo    |
|---|---|---|---|
| **BaseUri**     | cadeia          | URI do seu pacote de implementação de base| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **username**    | cadeia          | Novo nome de utilizador aleatório.| admin68876      |
| **password**    | cadeia segura    | Nova palavra-passe aleatória | LP! ACS\^2kh     |
| **Id de sessão**   | cadeia          | Sessão de versão de teste exclusiva ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>o nome de utilizador

Test-Drive inicializa este parâmetro com um **Uri de Base** do seu pacote de implementação, pelo que pode utilizar este parâmetro para construir o Uri de qualquer arquivo contido em seu pacote.

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

Dentro de seu modelo, pode utilizar este parâmetro para construir um Uri de qualquer ficheiro a partir do seu pacote de implementação da versão de teste. O exemplo abaixo mostra como construir um Uri do modelo ligado:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>o nome de utilizador

Test-Drive inicializa este parâmetro com um novo nome de usuário aleatório:

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

Valor de exemplo:

    admin68876

Pode utilizar nomes de utilizador aleatório ou constante para a sua solução.

#### <a name="password"></a>palavra-passe

Test-Drive inicializa este parâmetro com uma nova palavra-passe aleatória:

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

Valor de exemplo:

    Lp!ACS^2kh

Pode utilizar palavras-passe aleatórias ou constante para a sua solução.

#### <a name="session-id"></a>ID de sessão

Test-Drive inicializar este parâmetro com um GUID exclusivo que representa a ID de sessão de teste de unidade:

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

Valor de exemplo:

    b8c8693e-5673-449c-badd-257a405a6dee

Pode utilizar este parâmetro para identificar exclusivamente a sessão de teste de unidade, se for necessário.

### <a name="unique-names"></a>Nomes exclusivos

Alguns recursos do Azure, como contas de armazenamento ou nomes DNS, exige nomes exclusivos.

Isso significa que sempre que o Test Drive implanta o modelo do Resource Manager, ele cria um **novo grupo de recursos com um nome exclusivo** para todos os seus\' recursos. Portanto, é necessário para utilizar o [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) função concatenada com seus nomes de variáveis no grupo de recursos IDs para gerar valores exclusivos aleatórios:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Certifique-se de que concatenar as cadeias de caracteres de parâmetro/variável (\'contosovm\') com uma saída de cadeia de caracteres exclusivo (\'resourceGroup () Direi\'), porque Isto garante a exclusividade e a confiabilidade de cada variável.

Por exemplo, a maioria dos nomes de recursos não podem começar com um dígito, mas a função de cadeia de caracteres exclusivo pode retornar uma cadeia de caracteres, que começa com um dígito. Então, se utilizar a saída de cadeia de caracteres bruta exclusivo, as implementações irão falhar. 

Pode encontrar informações adicionais sobre as regras de nomenclatura de recursos e restrições nas [este artigo](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions).

### <a name="deployment-location"></a>Localização de implementação

Pode torná-lo Test-Drive disponíveis em diferentes regiões do Azure. A idéia é permitir que um utilizador escolher a região mais próxima, para fornecer a experiência do usuário beast.

Quando a versão de teste cria uma instância do laboratório, cria sempre um grupo de recursos em escolher a região, por um utilizador e, em seguida, executa seu modelo de implementação neste contexto de grupo. Então, o modelo deve escolher a localização de implementação do grupo de recursos:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

E, em seguida, utilizar esta localização para todos os recursos de uma instância específica de laboratório:

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

Tem de certificar-se de que a sua subscrição tem permissões para implementar todos os recursos que pretende implementar em cada uma das regiões do que se está a selecionar. Além disso, terá de certificar-se de que suas imagens de máquina virtual estão disponíveis em todas as regiões vai ativar, caso contrário, o modelo de implementação não irá funcionar para algumas regiões.

### <a name="outputs"></a>Saídas

Normalmente com modelos do Resource Manager, pode implementar sem a produzir qualquer saída. Isso é porque sabe que todos os valores a que utilizar para preencher os parâmetros do modelo e sempre manualmente pode inspecionar as propriedades de qualquer recurso.

Para o teste de unidade modelos do Resource Manager, no entanto, ele\'s importante para regressar à versão de teste todas as informações, que é necessária para obter um acesso para o laboratório (URIs de Web site, Máquina Virtual nomes de anfitrião, os nomes de utilizador e palavras-passe). Certifique-se de que todos os seus nomes de saída são legíveis porque estas variáveis são apresentadas para o cliente.

Não há qualquer restrições relacionadas com a saídas do modelo. Lembre-se converte de Test-Drive todos os resultados de valores em **cadeias de caracteres**, por isso, se enviar um objeto à saída, o usuário verá JSON cadeia de caracteres.

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

Mais uma coisa que deve levar em consideração é a subscrição e limites de serviço. Por exemplo, se quiser implementar até dez máquinas de virtuais de 4 núcleos, terá de certificar-se de que a subscrição que utiliza para seu laboratório permite-lhe utilizar 40 núcleos.

Pode encontrar mais informações sobre a subscrição do Azure e limites de serviço do [este artigo](https://docs.microsoft.com/azure/azure-subscription-service-limits). Como várias versões de teste podem ser realizadas ao mesmo tempo, certifique-se de que a sua subscrição pode lidar com o \# de núcleos, multiplicados pelo número total de simultâneas versões de teste que podem ser realizadas.

### <a name="what-to-upload"></a>O que carregar

Modelo de teste de unidade Resource Manager é carregado como um arquivo zip, que pode incluir vários artefatos de implementação, mas tem de ter um ficheiro com o nome **main-Template**. Este ficheiro é o modelo de implementação Azure Resource Manager e versão de teste utiliza-o para criar uma instância de um laboratório.

Se tiver recursos adicionais para além deste ficheiro, fazer referência a ele como um recurso externo dentro do modelo ou pode incluir o recurso no ficheiro zip.

Durante a certificação de publicação, o Test-Drive unzips seu pacote de implementação e coloca o respetivo conteúdo para um contentor de BLOBs de versão de teste interno. A estrutura de contentor reflete a estrutura do seu pacote de implementação:

| Package                       | Contentor de BLOBs de unidade de teste         |
|---|---|
| Template de principal                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| Templates/Solution.JSON           | https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json |
| scripts/warmup.ps1                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1  |


Chamamos um Uri de Uri de Base para o contentor de Blobs. Cada revisão do seu laboratório tem seu próprio contentor de BLOBs e, portanto, cada revisão do seu laboratório tem seu próprio Uri de Base. Versão de teste pode passar um Uri de Base do seu pacote de implementação descompactado no seu modelo através de parâmetros do modelo.

## <a name="transforming-template-examples-for-test-drive"></a>Transformar os exemplos de modelo para a versão de teste

O processo de transformar uma arquitetura de recursos num teste de unidade modelo do Resource Manager pode ser assustador. Para ajudar a facilitar esse processo, iremos\'ve feita exemplos sobre como fazer o melhor [transformar aqui os modelos de implementação atual](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Agora que tem a sua versão de teste criada, esta secção descreve cada um dos campos necessários para a publicação com êxito a sua versão de teste.

![Ativar a versão de teste na interface do usuário](./media/azure-resource-manager-test-drive/howtopub1.png)

O campo de primeiro e mais importante é ativar/desativar se pretende que o Test Drive ativado para a sua oferta, ou não. Quando seleciona **Sim,** o restante do formulário com todos os campos obrigatórios são apresentadas para que possa preencher. Quando seleciona **não,** o formulário torna-se desativado e se voltar a publicar com o Test-Drive desativada, a sua versão de teste é removido da produção.

Nota: Se existirem quaisquer testes de unidades utilizadas ativamente por utilizadores, essas versões de teste irá continuar a ser executado até a sessão expira.

### <a name="details"></a>Detalhes

A secção seguinte para preencher é que oferecem os detalhes sobre a sua versão de teste.

![Informações detalhadas de Test-Drive](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrição -** *necessário* é onde escrever a descrição principal sobre o que há em sua versão de teste. O cliente virá aqui para ler quais cenários sua versão de teste irá abordar sobre seu produto. 

**Manual do usuário -** *necessário* este é o passo a passo detalhada de sua experiência de versão de teste. O cliente abrirá isso e pode percorrer exatamente o que deseja fazer em toda a unidade de teste. É importante que este conteúdo é fácil de compreender e siga! (Tem de ser um ficheiro. pdf)

**Testar o vídeo de demonstração de unidade -** *recomendado* é semelhante para o Manual do utilizador, é melhor incluir um tutorial em vídeo de sua experiência de versão de teste. O cliente irá observar neste anterior ou durante a sua versão de teste e pode percorrer exatamente o que deseja fazer em toda a unidade de teste. É importante que este conteúdo é fácil de compreender e siga!

- **Nome** -título do seu vídeo
- **Ligação** -tem de ser um URL incorporado do tube ou vídeo. Exemplo sobre como obter o url do embedded está ilustrado abaixo:
- **Miniatura** -tem de ser uma imagem de alta qualidade pixels (533 x 324). Recomenda-se para tirar uma captura de ecrã de certa parte de sua experiência de Test-Drive aqui.

Segue-se como estes campos aparecem para o seu cliente durante a sua experiência de versão de teste.

![Localização dos campos de Test-Drive na oferta do Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuração técnica

A secção seguinte para preencher é onde carregue o modelo de Gestor de recursos de unidade de teste e define especificamente como sua versão de teste instâncias de trabalho.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instâncias -** *necessário* é onde configura quantas instâncias quiser, em que regiões e rapidez os seus clientes podem obter a versão de teste.

- **Instâncias** -selecione o regiões é onde escolhe em que o seu modelo de Gestor de recursos de unidade de teste é implementado no. Recomenda-se apenas selecionar uma região onde mais espera que os clientes podem ser localizado em.
- **Frequente** -aceder a instâncias de número de teste de unidade de mensagens em fila que já estão implementados e espera por região selecionada. Os clientes podem aceder de forma instantânea este versões de teste, em vez de ter de esperar por uma implementação. A desvantagem é que estas instâncias são sempre em execução na sua subscrição do Azure, para que eles incorre num tempo de atividade maior custos. Ele é altamente recomendado que tenha **pelo menos uma instância de acesso frequente**, como a maioria dos seus clientes não queria esperar para implementações de completas para concluir, e portanto, há uma entrega na utilização do cliente.
- **Quente** - número de unidade de teste instâncias por região que foram implementadas e, em seguida, a VM foi parada e armazenada no armazenamento do Azure. O tempo de espera para instâncias de acesso pouco frequente é mais lento do que instâncias de acesso frequente, mas também é mais barato o custo de tempo de atividade de armazenamento.
- **Frio** -instâncias do número de unidade de teste por região, possivelmente, pode ser implementado. Instâncias de frios requerem o modelo de Gestor de recursos de teste da unidade inteira de passar por uma implementação no momento de um cliente solicitar a versão de teste, pelo que é mais lento do que o acesso frequente ou pouco frequente instâncias. No entanto, a compensação é que apenas tem de pagar a duração da unidade de teste.

Neste momento calcula o número total de potenciais simultâneas versões de teste que vai para disponibilizar e certifique-se de que seu limite de quota para a sua subscrição pode lidar com essa quantidade simultânea:

**(Número de regiões selecionadas em x instâncias de acesso frequente) + (número de regiões selecionadas x instâncias de acesso pouco frequente) + (número de regiões selecionadas x frios instâncias)**

**Teste de unidade de duração (horas) -** *necessário* duração para o tempo que o Test-Drive permanecerá ativo, em \# de horas. O Test-Drive automaticamente termina após o final deste período de tempo.

**Modelo de teste de unidade Resource Manager -** *necessário* carregue o modelo do Resource Manager aqui. Este é o ficheiro que criou na secção anterior acima. Nomeie o arquivo de modelo principal: "main-Template" e certifique-se de que o modelo do Resource Manager contém parâmetros de saída para variáveis-chave que são necessários. (Tem de ser um ficheiro. zip)

**Aceder a informações -** *necessário* depois de um cliente recebe a unidade de teste, as informações de acesso são apresentadas aos mesmos. Estas instruções destinam-se para partilhar os parâmetros de saída útil do seu teste de unidade modelo do Resource Manager. Para incluir parâmetros de saída, de utilizar chavetas aspas (por exemplo, **{{outputname}}**), e eles serão inseridos corretamente na localização. (Formatação da cadeia de caracteres HTML é recomendado aqui para renderizar no front-end).

### <a name="test-drive-deployment-subscription-details"></a>Detalhes de subscrição de implementação de unidade de teste

A seção final para preencher é ser capaz de implantar as versões de teste automaticamente ao ligar-se a sua subscrição do Azure e o Azure Active Directory (AD).

![Detalhes de subscrição de implementação de unidade do teste](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID de subscrição do Azure -** *necessário* isso concede acesso a serviços do Azure e o portal do Azure. A subscrição está em que é comunicada a utilização de recursos e serviços são cobrados. Se ainda não tiver uma **separado** subscrição do Azure para versões de teste apenas, vá em frente e criar uma. Pode encontrar os Ids de subscrição do Azure iniciando sessão no portal do Azure e navegando para as subscrições no menu da esquerda. (Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subscrições do Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID de inquilino do Azure AD -** *necessário* se tiver um ID de inquilino já disponível que pode encontrá-lo abaixo nas propriedades -\> ID do diretório.

![Propriedades do Active Directory do Azure](./media/azure-resource-manager-test-drive/subdetails3.png)

Caso contrário, crie um novo inquilino no Azure Active Directory.

![Inquilinos de lista do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definir a organização, o domínio e o país para o inquilino do Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Confirme a seleção](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID de aplicação do Azure AD -** *necessário* próxima etapa é criar e registar uma nova aplicação. Utilizaremos esta aplicação para realizar operações na sua instância de teste de unidade.

1. Navegue para o diretório recentemente criado ou já existente diretório e selecione o Azure Active directory no painel de filtro.
2. Procure "Registos de aplicações" e clique em "Adicionar"
3. Forneça um nome de aplicação.
4. Selecione o tipo de como "aplicação Web / API"
5. Forneça qualquer valor no URL de início de sessão, ganhamos\'t estar a utilizar esse campo.
6. Clique em criar.
7. Depois do aplicativo tiver sido criado, vá para propriedades -\> definir a aplicação como multi-inquilino e clique em Save.

Clique em Guardar. A última etapa é obter o ID da aplicação para esta aplicação registada e cole-o no campo da versão de teste aqui.

![Detalhes de ID de aplicação do Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Tendo em conta que estiver a utilizar a aplicação para implementar para a subscrição, precisamos de adicionar a aplicação como um contribuinte da subscrição. As instruções para estas são como abaixo:

1. Navegue para o painel de subscrições e selecione a subscrição adequada, que está a utilizar para a versão de teste apenas.
1. Clique em **controlo de acesso (IAM)**.
1. Clique nas **atribuições de funções** separador.  ![Adicionar um novo principal de controlo de acesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Clique em **adicionar atribuição de função**.
1. Definir a função como **contribuinte**.
1. Escreva o nome de aplicação do Azure AD e selecione a aplicação para atribuir a função.
    ![Adicione as permissões](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Clique em **Guardar**.

**Chave da aplicação do Azure AD -** *necessário* o campo final é gerar uma chave de autenticação. Em chaves, adicione uma descrição de chave, defina a duração para nunca expirar, em seguida, selecione guardar. É **importante** para evitar que um expiradas chave, que irá interromper a sua versão de teste em produção. Copie este valor e cole-o no seu campo obrigatório da versão de teste.

![Mostra as chaves para a aplicação do Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que tem todos os campos de Test-Drive preenchidos, passar por e **voltar a publicar** sua oferta. Assim que a sua versão de teste passou a certificação, deve passar um extensivamente testar a experiência do cliente no **pré-visualização** da sua oferta. Iniciar uma versão de teste na interface de Usuário e, em seguida, abra a sua subscrição do Azure no portal do Azure e certifique-se de que as versões de teste estão a ser totalmente implementados corretamente.

![Portal do Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

É importante ter em atenção que não elimina quaisquer instâncias de versão de teste como terem sido aprovisionados para os seus clientes para que o serviço de versão de teste irá limpar automaticamente estes grupos de recursos depois de um cliente tenha terminado com ele.

Assim que se sentir à vontade com a sua oferta de pré-visualização, agora é hora de **implemente**! Há um processo de revisão final da Microsoft, assim que a oferta foi publicada duplique verificação toda a experiência de ponta a ponta. Se por algum motivo, a oferta é rejeitada, enviaremos uma notificação para o contacto de engenharia da sua oferta explicando o que será necessário obter corrigidos.

Se tiver mais perguntas, estiver à procura de conselhos de resolução de problemas ou pretende tornar a sua versão de teste mais bem-sucedida, vá até [FAQ, resolução de problemas e melhores práticas](./marketing-and-best-practices.md).
