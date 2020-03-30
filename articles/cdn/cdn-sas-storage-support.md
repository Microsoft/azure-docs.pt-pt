---
title: Utilização de CDN Azure com SAS / Microsoft Docs
description: A Azure CDN apoia a utilização da Assinatura de Acesso Partilhado (SAS) para conceder acesso limitado a contentores de armazenamento privados.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: e7a170eaf74531cf4bd8c28aafaa5873f2459d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69982421"
---
# <a name="using-azure-cdn-with-sas"></a>Utilização de CDN Azure com SAS

Quando configurar uma conta de armazenamento para a Rede de Entrega de Conteúdos Azure (CDN) para utilizar para cache conteúdo, por padrão, qualquer pessoa que conheça os URLs para os seus recipientes de armazenamento pode aceder aos ficheiros que carregou. Para proteger os ficheiros na sua conta de armazenamento, pode definir o acesso dos seus recipientes de armazenamento de público para privado. No entanto, se o fizer, ninguém poderá aceder aos seus ficheiros. 

Se quiser conceder acesso limitado aos contentores de armazenamento privado, pode utilizar a funcionalidade Assinatura de Acesso Partilhado (SAS) na sua conta de armazenamento do Azure. Uma SAS é um URI que concede direitos de acesso restritos aos recursos de Armazenamento do Azure sem expor a sua chave de conta. Pode fornecer um SAS aos clientes em que não confia na chave da sua conta de armazenamento, mas a quem pretende delegar acesso a determinados recursos da conta de armazenamento. Distribuindo uma assinatura de acesso partilhado URI a estes clientes, você concede-lhes acesso a um recurso por um determinado período de tempo.
 
Com um SAS, pode definir vários parâmetros de acesso a uma bolha, tais como horários de início e expiração, permissões (leitura/escrita) e intervalos IP. Este artigo descreve como usar o SAS em conjunto com o Azure CDN. Para obter mais informações sobre o SAS, incluindo como criá-lo e as suas opções de parâmetros, consulte A Utilização de [assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Criação de CDN Azure para trabalhar com o armazenamento SAS
Recomenda-se a utilização das três opções seguintes para a utilização do SAS com o Azure CDN. Todas as opções assumem que já criou um SAS em funcionamento (ver pré-requisitos). 
 
### <a name="prerequisites"></a>Pré-requisitos
Para começar, crie uma conta de armazenamento e, em seguida, gere um SAS para o seu ativo. Pode gerar dois tipos de assinaturas de acesso armazenadas: um Serviço SAS ou uma conta SAS. Para obter mais informações, consulte [Tipos de assinaturas de acesso partilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Depois de ter gerado um token SAS, pode aceder ao `?sv=<SAS token>` seu ficheiro de armazenamento blob através da sua URL. Este URL tem o seguinte formato: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Por exemplo:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para obter mais informações sobre a definição de parâmetros, consulte considerações de [parâmetros SAS](#sas-parameter-considerations) e [parâmetros](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works)de assinatura de acesso partilhado .

![Definições de CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opção 1: Utilização de SAS com passagem para armazenamento de blob do Azure CDN

Esta opção é a mais simples e utiliza um único token SAS, que é passado do Azure CDN para o servidor de origem.
 
1. Selecione um ponto final, selecione **regras de Caching**e, em seguida, selecione **Cache cada URL único** da lista de cache de cordas de **Consulta.**

    ![Regras de colocação em cache da CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Depois de configurar o SAS na sua conta de armazenamento, deve utilizar o token SAS com os URLs do ponto final e do servidor de origem CDN para aceder ao ficheiro. 
   
   O URL final do ponto final cdn resultante tem o seguinte formato:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Por exemplo:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ajuste a duração da cache utilizando regras `Cache-Control` de cache ou adicionando cabeçalhos no servidor de origem. Uma vez que o Azure CDN trata o token SAS como uma simples corda de consulta, como uma boa prática deve configurar uma duração de cache que expira no ou antes do tempo de validade do SAS. Caso contrário, se um ficheiro for colocado em cache por uma duração mais longa do que o SAS estiver ativo, o ficheiro poderá ser acessível a partir do servidor de origem Azure CDN após o tempo de validade do SAS ter decorrido. Se esta situação ocorrer e pretender tornar o seu ficheiro em cache inacessível, deve efetuar uma operação de purga no ficheiro para o limpar da cache. Para obter informações sobre a definição da duração da cache no Azure CDN, consulte [controle azure CDN comportamento de cache com regras](cdn-caching-rules.md)de cache .

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opção 2: Ficha oculta do CDN SAS utilizando uma regra de reescrita
 
Esta opção está disponível apenas para **Azure CDN Premium a partir dos** perfis verizon. Com esta opção, pode fixar o armazenamento de blob no servidor de origem. Pode querer utilizar esta opção se não precisar de restrições de acesso específicas para o ficheiro, mas pretende impedir que os utilizadores acedam diretamente à origem do armazenamento para melhorar os tempos de descarga do Azure CDN. O token SAS, que é desconhecido para o utilizador, é necessário para qualquer pessoa que aceda a ficheiros no recipiente especificado do servidor de origem. No entanto, devido à regra de reescrita do URL, a ficha SAS não é necessária no ponto final do CDN.
 
1. Utilize o [motor de regras](cdn-rules-engine.md) para criar uma regra de reescrita url. As novas regras demoram até 4 horas a propagar-se.

   ![Botão de gestão do CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN governa botão do motor](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   A seguinte regra url reescrita utiliza um padrão de expressão regular com um grupo de captura e um ponto final chamado *sasstoragedemo:*
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de reescrita do](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![URL CDN - regra de reescrita de URL CDN esquerdo - à direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Após a nova regra se tornar ativa, qualquer pessoa pode aceder a ficheiros no recipiente especificado no ponto final do CDN, independentemente de estarem a usar um token SAS no URL. Aqui está o formato:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Por exemplo:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Ajuste a duração da cache utilizando regras `Cache-Control` de cache ou adicionando cabeçalhos no servidor de origem. Uma vez que o Azure CDN trata o token SAS como uma simples corda de consulta, como uma boa prática deve configurar uma duração de cache que expira no ou antes do tempo de validade do SAS. Caso contrário, se um ficheiro for colocado em cache por uma duração mais longa do que o SAS estiver ativo, o ficheiro poderá ser acessível a partir do servidor de origem Azure CDN após o tempo de validade do SAS ter decorrido. Se esta situação ocorrer e pretender tornar o seu ficheiro em cache inacessível, deve efetuar uma operação de purga no ficheiro para o limpar da cache. Para obter informações sobre a definição da duração da cache no Azure CDN, consulte [controle azure CDN comportamento de cache com regras](cdn-caching-rules.md)de cache .

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opção 3: Utilização da autenticação token de segurança CDN com uma regra de reescrita

Para utilizar a autenticação de token de segurança Azure CDN, deve ter um **Azure CDN Premium a partir do** perfil Verizon. Esta opção é a mais segura e personalizável. O acesso ao cliente baseia-se nos parâmetros de segurança que definiu no símbolo de segurança. Uma vez criado e configurado o símbolo de segurança, será necessário em todos os URLs de ponto final da CDN. No entanto, devido à regra de reescrita do URL, a ficha SAS não é necessária no ponto final do CDN. Se o token SAS mais tarde se tornar inválido, o Azure CDN deixará de ser capaz de revalidar o conteúdo do servidor de origem.

1. [Crie um token de segurança Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) e ative-o utilizando o motor de regras para o ponto final do CDN e caminho onde os seus utilizadores possam aceder ao ficheiro.

   Um URL de ponto final de símbolo de segurança tem o seguinte formato:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Por exemplo:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   As opções de parâmetros para uma autenticação token de segurança são diferentes das opções de parâmetros para um token SAS. Se optar por utilizar um tempo de validade quando criar um símbolo de segurança, deverá defini-lo ao mesmo valor que o tempo de validade do token SAS. Ao fazê-lo, assegura-se de que o tempo de validade é previsível. 
 
2. Utilize o [motor de regras](cdn-rules-engine.md) para criar uma regra de reescrita URL para permitir o acesso simbólico sas a todas as bolhas do recipiente. As novas regras demoram até 4 horas a propagar-se.

   A seguinte regra url reescrita utiliza um padrão de expressão regular com um grupo de captura e um ponto final chamado *sasstoragedemo:*
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de reescrita do](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![URL CDN - regra de reescrita de URL CDN esquerdo - à direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Se renovar o SAS, certifique-se de que atualiza a regra de Reescrita do Url com o novo token SAS. 

## <a name="sas-parameter-considerations"></a>Considerações de parâmetros SAS

Como os parâmetros SAS não são visíveis para o Azure CDN, o Azure CDN não pode alterar o seu comportamento de entrega com base neles. As restrições definidas paraparâmetros aplicam-se apenas aos pedidos que o Azure CDN faz ao servidor de origem, e não aos pedidos do cliente ao Azure CDN. Esta distinção é importante de considerar quando definir parâmetros SAS. Se estas capacidades avançadas forem necessárias e estiver a utilizar a [Opção 3,](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)detetete as restrições adequadas no símbolo de segurança Azure CDN.

| Nome do parâmetro SAS | Descrição |
| --- | --- |
| Iniciar | O tempo que o Azure CDN pode começar a aceder ao ficheiro blob. Devido ao desvio do relógio (quando um sinal de relógio chega em diferentes momentos para diferentes componentes), escolha uma hora 15 minutos antes se quiser que o ativo esteja disponível imediatamente. |
| Fim | O tempo após o qual o Azure CDN já não consegue aceder ao ficheiro blob. Os ficheiros anteriormente emcache no Azure CDN ainda estão acessíveis. Para controlar o tempo de validade do ficheiro, ou detete o tempo de validade adequado no token de segurança do CDN Azure ou expurgar o ativo. |
| Endereços IP permitidos | Opcional. Se estiver a utilizar **o Azure CDN da Verizon,** pode definir este parâmetro para as gamas definidas no [Azure CDN a partir de Gamas IP do Servidor de Borda](/azure/cdn/cdn-pop-list-api)de Verizon . Se estiver a utilizar **o Azure CDN da Akamai,** não é possível definir o parâmetro de gamaIP porque os endereços IP não são estáticos.|
| Protocolos permitidos | O protocolo(s) permitido para um pedido feito com a conta SAS. Recomenda-se a definição HTTPS.|

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o SAS, consulte os seguintes artigos:
- [Utilizar assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Assinaturas de acesso partilhado, Parte 2: Criar e utilizar um SAS com armazenamento Blob](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Para obter mais informações sobre a criação de autenticação simbólica, consulte a Segurança dos Ativos da Rede de [Entrega de Conteúdos Azure com autenticação simbólica.](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
