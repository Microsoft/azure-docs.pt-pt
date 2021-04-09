---
title: Utilização de Azure CDN com sas | Microsoft Docs
description: A Azure CDN apoia a utilização de Assinatura de Acesso Partilhado (SAS) para conceder acesso limitado a contentores de armazenamento privados.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725327"
---
# <a name="using-azure-cdn-with-sas"></a>Utilização de Azure CDN com SAS

Quando configurar uma conta de armazenamento para a Azure Content Delivery Network (CDN) para utilizar para cache de conteúdo, por padrão qualquer pessoa que conheça os URLs para os seus recipientes de armazenamento pode aceder aos ficheiros que fez o upload. Para proteger os ficheiros na sua conta de armazenamento, pode definir o acesso dos seus recipientes de armazenamento de público a privado. No entanto, se o fizer, ninguém poderá aceder aos seus ficheiros. 

Se quiser conceder acesso limitado aos contentores de armazenamento privado, pode utilizar a funcionalidade Assinatura de Acesso Partilhado (SAS) na sua conta de armazenamento do Azure. Uma SAS é um URI que concede direitos de acesso restritos aos recursos de Armazenamento do Azure sem expor a sua chave de conta. Pode fornecer um SAS a clientes que não confia na chave da sua conta de armazenamento, mas a quem pretende delegar o acesso a determinados recursos da conta de armazenamento. Ao distribuir uma assinatura de acesso partilhado URI a estes clientes, concede-lhes acesso a um recurso por um determinado período de tempo.
 
Com um SAS, pode definir vários parâmetros de acesso a uma bolha, tais como tempos de início e expiração, permissões (leitura/escrita) e intervalos de IP. Este artigo descreve como usar SAS em conjunto com a Azure CDN. Para obter mais informações sobre o SAS, incluindo como criá-lo e as suas opções de [parâmetros, consulte utilizar assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Criação do Azure CDN para trabalhar com o armazenamento SAS
Recomenda-se a utilização de SAS com Azure CDN. Todas as opções assumem que já criou um SAS em funcionamento (ver pré-requisitos). 
 
### <a name="prerequisites"></a>Pré-requisitos
Para começar, crie uma conta de armazenamento e, em seguida, gere um SAS para o seu ativo. Pode gerar dois tipos de assinaturas de acesso armazenadas: um serviço SAS ou uma conta SAS. Para mais informações, consulte [tipos de assinaturas de acesso partilhado.](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures)

Depois de ter gerado um token SAS, pode aceder ao seu ficheiro de armazenamento de bolhas, `?sv=<SAS token>` anexando-o ao seu URL. Este URL tem o seguinte formato: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Por exemplo:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para obter mais informações sobre a definição de parâmetros, consulte [considerações de parâmetros SAS](#sas-parameter-considerations) e [parâmetros de assinatura de acesso partilhado](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works).

![Definições CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opção 1: Utilização de SAS com pass-through para blob armazenamento a partir de Azure CDN

Esta opção é a mais simples e utiliza um único token SAS, que é passado de Azure CDN para o servidor de origem.
 
1. Selecione um ponto final, selecione **as regras de Caching** e, em seguida, selecione **Cache todos os URL exclusivos** da lista **de caching de cordas de consulta.**

    ![Regras de colocação em cache da CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Depois de configurar o SAS na sua conta de armazenamento, tem de utilizar o token SAS com os URLs do servidor de ponta e origem cdN para aceder ao ficheiro. 
   
   O URL de ponta CDN resultante tem o seguinte formato: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Por exemplo:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Afinar a duração da cache utilizando regras de cache ou adicionando `Cache-Control` cabeçalhos no servidor de origem. Como o Azure CDN trata o token SAS como uma simples cadeia de consulta, como uma boa prática deve configurar uma duração de caching que expire dentro ou antes do tempo de expiração do SAS. Caso contrário, se um ficheiro for em cache por uma duração mais longa do que o SAS está ativo, o ficheiro pode estar acessível a partir do servidor de origem Azure CDN após o tempo de expiração do SAS ter decorrido. Se esta situação ocorrer e pretender tornar o seu ficheiro em cache inacessível, tem de efetuar uma operação de purga no ficheiro para o retirar da cache. Para obter informações sobre a definição da duração da cache no Azure CDN, consulte [o comportamento de cache do Control Azure CDN com regras de cache](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opção 2: Símbolo SSS SN ESCONDIDO usando uma regra de reescrita
 
Esta opção está disponível apenas para **Azure CDN Premium a partir de** perfis Verizon. Com esta opção, pode proteger o armazenamento de bolhas no servidor de origem. Pode querer utilizar esta opção se não necessitar de restrições de acesso específicas para o ficheiro, mas pretende impedir que os utilizadores acedam diretamente à origem do armazenamento para melhorar os tempos de descarregamento do Azure CDN. O token SAS, que é desconhecido do utilizador, é necessário para qualquer pessoa que aceda a ficheiros no recipiente especificado do servidor de origem. No entanto, devido à regra url rewrite, o token SAS não é necessário no ponto final do CDN.
 
1. Utilize o [motor de regras](./cdn-verizon-premium-rules-engine.md) para criar uma regra de reescrita de URL. As novas regras demoram até 4 horas a propagar-se.

   ![Botão de gestão CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN regras botão do motor](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   A seguinte regra url rewrite amostra utiliza um padrão de expressão regular com um grupo de captura e um ponto final chamado *sasstoragedemo*:
   
   Origem:   
   `(container1/.*)`


   Destino:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de reescrita de URL CDN - regra de ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ reescrita de URL cdn esquerda - direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Após a nova regra ficar ativa, qualquer pessoa pode aceder a ficheiros no recipiente especificado no ponto final da CDN, independentemente de estarem a utilizar um token SAS no URL. Aqui está o formato: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Por exemplo:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Afinar a duração da cache utilizando regras de cache ou adicionando `Cache-Control` cabeçalhos no servidor de origem. Como o Azure CDN trata o token SAS como uma simples cadeia de consulta, como uma boa prática deve configurar uma duração de caching que expire dentro ou antes do tempo de expiração do SAS. Caso contrário, se um ficheiro for em cache por uma duração mais longa do que o SAS está ativo, o ficheiro pode estar acessível a partir do ponto final Azure CDN após o tempo de expiração do SAS ter decorrido. Se esta situação ocorrer e pretender tornar o seu ficheiro em cache inacessível, tem de efetuar uma operação de purga no ficheiro para o retirar da cache. Para obter informações sobre a definição da duração da cache no Azure CDN, consulte [o comportamento de cache do Control Azure CDN com regras de cache](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opção 3: Utilização da autenticação simbólica de símbolo de segurança CDN com regra de reescrita

Para utilizar a autenticação simbólica de segurança Azure CDN, tem de ter um **Azure CDN Premium do** perfil Verizon. Esta opção é a mais segura e personalizável. O acesso ao cliente baseia-se nos parâmetros de segurança que definiu no token de segurança. Uma vez criado e configurado o token de segurança, será necessário em todos os URLs de ponto final da CDN. No entanto, devido à regra url rewrite, o token SAS não é necessário no ponto final do CDN. Se o token SAS se tornar mais tarde inválido, o Azure CDN deixará de ser capaz de revalidar o conteúdo do servidor de origem.

1. [Crie um token de segurança Azure CDN](./cdn-token-auth.md#setting-up-token-authentication) e ative-o utilizando o motor de regras para o ponto final cdn e caminho onde os seus utilizadores possam aceder ao ficheiro.

   Um URL de ponto final simbólico de segurança tem o seguinte formato:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Por exemplo:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   As opções de parâmetro para uma autenticação de símbolo de segurança são diferentes das opções de parâmetro para um token SAS. Se optar por utilizar um tempo de validade quando criar um token de segurança, deverá defini-lo para o mesmo valor que o tempo de validade do token SAS. Ao fazê-lo, garante que o tempo de validade é previsível. 
 
2. Utilize o [motor de regras](./cdn-verizon-premium-rules-engine.md) para criar uma regra de reescrita de URL para permitir o acesso do símbolo SAS a todas as bolhas no recipiente. As novas regras demoram até 4 horas a propagar-se.

   A seguinte regra url rewrite amostra utiliza um padrão de expressão regular com um grupo de captura e um ponto final chamado *sasstoragedemo*:
   
   Origem:   
   `(container1/.*)`
   
   Destino:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de reescrita de URL CDN - regra de ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ reescrita de URL cdn esquerda - direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Se renovar o SAS, certifique-se de que atualiza a regra url Rewrite com o novo token SAS. 

## <a name="sas-parameter-considerations"></a>Considerações de parâmetro SAS

Como os parâmetros SAS não são visíveis para a Azure CDN, a Azure CDN não pode alterar o seu comportamento de entrega com base neles. As restrições de parâmetros definidas aplicam-se apenas aos pedidos que a Azure CDN faz ao servidor de origem, e não a pedidos do cliente para a Azure CDN. Esta distinção é importante de considerar quando define os parâmetros SAS. Se estas capacidades avançadas forem necessárias e estiver a utilizar [a Opção 3,](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)desacorda as restrições adequadas no token de segurança Azure CDN.

| Nome do parâmetro SAS | Description |
| --- | --- |
| Iniciar | O tempo que a Azure CDN pode começar a aceder ao ficheiro blob. Devido ao desvio do relógio (quando um sinal do relógio chega em diferentes horas para diferentes componentes), escolha uma hora 15 minutos antes se quiser que o ativo esteja disponível imediatamente. |
| Fim | O tempo após o qual a Azure CDN já não pode aceder ao ficheiro blob. Os ficheiros previamente em cache no Azure CDN ainda estão acessíveis. Para controlar o prazo de validade do ficheiro, ou desvenda o prazo de validade adequado no token de segurança Azure CDN ou purga o ativo. |
| Endereços IP permitidos | Opcional. Se estiver a utilizar **o Azure CDN da Verizon,** pode definir este parâmetro para as gamas definidas no [Azure CDN a partir de Verizon Edge Server IP Ranges](./cdn-pop-list-api.md). Se estiver a utilizar **o Azure CDN da Akamai,** não é possível definir o parâmetro de gama IP porque os endereços IP não são estáticos.|
| Protocolos permitidos | O protocolo permitiu um pedido feito com a conta SAS. Recomenda-se a definição HTTPS.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a SAS, consulte os seguintes artigos:
- [Utilizar assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md)
- [Assinaturas de acesso compartilhado, Parte 2: Criar e utilizar um SAS com armazenamento Blob](../storage/common/storage-sas-overview.md)

Para obter mais informações sobre a configuração da autenticação simbólica, consulte [os ativos da Rede de Entrega de Conteúdos Azure com autenticação simbólica](./cdn-token-auth.md).