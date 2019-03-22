---
title: Utilizar a CDN do Azure com SAS | Documentos da Microsoft
description: A CDN do Azure suporta a utilização de acesso assinatura partilhado (SAS) para conceder acesso limitado aos contentores de armazenamento privado.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: ee64b4cbfd024c91b226736bc8cac0b9b33f964e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170399"
---
# <a name="using-azure-cdn-with-sas"></a>Utilizar a CDN do Azure com SAS

Quando configura uma conta de armazenamento para entrega rede conteúdos (CDN) a utilizar para conteúdo em cache, por predefinição qualquer pessoa que sabe que os URLs para os seus contentores de armazenamento, pode aceder aos ficheiros que já carregou. Para proteger os ficheiros na sua conta de armazenamento, pode definir o acesso dos seus contentores de armazenamento do público para privada. No entanto, se fizer isso, ninguém será capaz de aceder aos seus ficheiros. 

Se quiser conceder acesso limitado aos contentores de armazenamento privado, pode utilizar a funcionalidade Assinatura de Acesso Partilhado (SAS) na sua conta de armazenamento do Azure. Uma SAS é um URI que concede direitos de acesso restritos aos recursos de Armazenamento do Azure sem expor a sua chave de conta. Pode fornecer uma SAS para os clientes que não seja fidedigna com a sua chave de conta de armazenamento, mas para quem pretende delegar acesso a determinados recursos de conta de armazenamento. Ao distribuir um URI de assinatura de acesso partilhado a estes clientes, pode conceder acesso a um recurso para um período de tempo especificado.
 
Com uma SAS, pode definir vários parâmetros de acesso para um blob, como tempos de início e de expiração, as permissões (leitura/escrita) e intervalos de IP. Este artigo descreve como utilizar a SAS em conjunto com a CDN do Azure. Para obter mais informações sobre SAS, incluindo como criá-lo e das opções de parâmetro, consulte [Using partilhado assinaturas de acesso (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configurar a CDN do Azure para trabalhar com o armazenamento SAS
As três opções seguintes são recomendadas para utilizar a SAS com a CDN do Azure. Todas as opções de partem do princípio de que já criou um SAS (consulte pré-requisitos) em funcionamento. 
 
### <a name="prerequisites"></a>Pré-requisitos
Para começar, crie uma conta de armazenamento e, em seguida, gerar uma SAS para o seu elemento. Pode gerar dois tipos de assinaturas de acesso armazenadas: um serviço SAS ou uma conta SAS. Para obter mais informações, consulte [tipos de assinaturas de acesso partilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Depois de ter gerado um token SAS, pode acessar o seu ficheiro de armazenamento de BLOBs, acrescentando `?sv=<SAS token>` ao seu URL. Este URL tem o seguinte formato: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Por exemplo:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para obter mais informações sobre parâmetros de configuração, consulte [considerações do parâmetro SAS](#sas-parameter-considerations) e [parâmetros de assinatura de acesso partilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Definições de SAS de CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opção 1: Utilizar a SAS com pass-through para armazenamento de Blobs do Azure CDN

Esta opção é a mais simples e utiliza um único token SAS, que é transmitido a partir da CDN do Azure para o servidor de origem.
 
1. Selecione um ponto de extremidade, selecione **regras de colocação em cache**, em seguida, selecione **colocar em Cache todos os URL exclusivos** partir os **colocação em cache de cadeia de caracteres de consulta** lista.

    ![Regras de colocação em cache de CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Depois de configurar SAS na sua conta de armazenamento, tem de utilizar o token SAS com os URLs de servidor de origem e de ponto final da CDN para aceder ao ficheiro. 
   
   O URL de ponto final CDN resultante tem o seguinte formato: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Por exemplo:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ajustar a duração de cache, ao utilizar regras de colocação em cache ou adicionando `Cache-Control` cabeçalhos para o servidor de origem. Como o CDN do Azure trata o token SAS, como uma cadeia de consulta simples, como melhor prática que deve configurar uma duração de colocação em cache que expire em ou antes da hora de expiração de SAS. Caso contrário, se um arquivo é armazenado em cache durante um longo período que o Active Directory a SAS, o ficheiro poderá estar acessível a partir do servidor de origem da CDN do Azure, após a hora de expiração de SAS decorrido. Se esta situação ocorre e pretender disponibilizar o ficheiro em cache inacessível, tem de efetuar uma operação de remoção do ficheiro de limpá-la a partir da cache. Para obter informações sobre como definir a duração de cache no CDN do Azure, consulte [comportamento de cache com regras de colocação em cache de CDN do Azure de controle](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opção 2: Token de SAS de CDN oculta, utilizando uma regra de reescrita
 
Esta opção só está disponível para **CDN do Azure Premium da Verizon** perfis. Com esta opção, pode proteger o armazenamento de BLOBs para o servidor de origem. Pode querer utilizar esta opção se não precisa de restrições de acesso específico para o ficheiro, mas quer impedir que os utilizadores acedam a origem de armazenamento diretamente para melhorar os tempos de descarga da CDN do Azure. O token SAS, o que é desconhecido para o usuário, é necessário para todas as pessoas a aceder a ficheiros no contentor especificado do servidor de origem. No entanto, devido a regra de reescrita de URLs, o token SAS não é necessário no ponto final da CDN.
 
1. Utilize o [motor de regras](cdn-rules-engine.md) para criar uma regra de reescrita de URLs. Novas regras demorar até quatro horas a serem propagadas.

   ![Botão de gerir a CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Botão de motor de regras CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   A regra de reescrita de URLs de exemplo seguinte utiliza um padrão de expressão regular com um grupo de captura e um ponto de extremidade com o nome *sasstoragedemo*:
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Rewrite regra - à esquerda](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![regra reescrita de URLs de CDN - direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Depois da nova regra é ativada, qualquer pessoa pode aceder a ficheiros no contentor especificado no ponto final da CDN, independentemente de se estiver usando um token SAS no URL. Este é o formato: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Por exemplo:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Ajustar a duração de cache, ao utilizar regras de colocação em cache ou adicionando `Cache-Control` cabeçalhos para o servidor de origem. Como o CDN do Azure trata o token SAS, como uma cadeia de consulta simples, como melhor prática que deve configurar uma duração de colocação em cache que expire em ou antes da hora de expiração de SAS. Caso contrário, se um arquivo é armazenado em cache durante um longo período que o Active Directory a SAS, o ficheiro poderá estar acessível a partir do servidor de origem da CDN do Azure, após a hora de expiração de SAS decorrido. Se esta situação ocorre e pretender disponibilizar o ficheiro em cache inacessível, tem de efetuar uma operação de remoção do ficheiro de limpá-la a partir da cache. Para obter informações sobre como definir a duração de cache no CDN do Azure, consulte [comportamento de cache com regras de colocação em cache de CDN do Azure de controle](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opção 3: Utilizar a autenticação de token de segurança CDN com uma regra de reescrita

Para utilizar a autenticação de token de segurança de CDN do Azure, tem de ter uma **CDN do Azure Premium da Verizon** perfil. Esta opção é a mais segura e personalizável. Acesso para cliente baseia-se nos parâmetros de segurança que definiu no token de segurança. Depois de criar e configurar o token de segurança, será necessário em todos os URLs de ponto final CDN. No entanto, devido a regra de reescrita de URLs, o token SAS não é necessário no ponto final da CDN. Se o token SAS mais tarde se torna inválido, o CDN do Azure já não poderá revalide o conteúdo do servidor de origem.

1. [Criar um token de segurança de CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) e ativá-lo por meio do mecanismo de regras para o ponto final da CDN e o caminho em que os utilizadores podem aceder ao ficheiro.

   Um URL de ponto final do token de segurança tem o seguinte formato:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Por exemplo:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   As opções de parâmetros para uma autenticação de token de segurança são diferentes as opções de parâmetros para um token SAS. Se optar por utilizar um prazo de expiração quando cria um token de segurança, deve configurá-lo para o mesmo valor que a hora de expiração para o token SAS. Isso faz com que a hora de expiração é previsível. 
 
2. Utilize o [motor de regras](cdn-rules-engine.md) para criar uma regra de reescrita de URLs de mensagens em fila para ativar o acesso de token de SAS para todos os blobs no contentor. Novas regras demorar até quatro horas a serem propagadas.

   A regra de reescrita de URLs de exemplo seguinte utiliza um padrão de expressão regular com um grupo de captura e um ponto de extremidade com o nome *sasstoragedemo*:
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Rewrite regra - à esquerda](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![regra reescrita de URLs de CDN - direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Se renovar a SAS, certifique-se de que atualizar a regra de reescrita de URLs com o novo token SAS. 

## <a name="sas-parameter-considerations"></a>Considerações de parâmetro SAS

Como parâmetros SAS não são visíveis para a CDN do Azure, a CDN do Azure não é possível alterar seu comportamento de entrega com base nos mesmos. As restrições de parâmetro definido aplicam-se apenas nos pedidos que torna a CDN do Azure para o servidor de origem, não para pedidos do cliente para a CDN do Azure. Essa distinção é importante considerar quando definir os parâmetros SAS. Se estas capacidades avançadas são necessárias e estiver a utilizar [opção 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), defina as restrições adequadas no token de segurança de CDN do Azure.

| Nome do parâmetro SAS | Descrição |
| --- | --- |
| Iniciar | O tempo que pode começar a CDN do Azure para aceder ao ficheiro de Blobs. Devido a relógio skew (quando é recebido um sinal de relógio em momentos diferentes para diferentes componentes), escolha uma hora 15 minutos anteriormente, se pretender que o recurso estejam disponíveis imediatamente. |
| Terminar | O tempo após o qual da CDN do Azure já não pode aceder ao ficheiro de Blobs. Anteriormente ficheiros em cache no CDN do Azure ainda estão acessíveis. Para controlar o tempo de expiração de ficheiros, defina a hora de expiração adequado no token de segurança de CDN do Azure ou remover o recurso. |
| Endereços IP permitidos | Opcional. Se estiver a utilizar **CDN do Azure da Verizon**, pode definir este parâmetro para os intervalos definidos na [CDN do Azure da Verizon Edge intervalos de IP servidor](https://msdn.microsoft.com/library/mt757330.aspx). Se estiver a utilizar **CDN do Azure da Akamai**, não é possível definir o parâmetro de intervalos IP, porque os endereços IP não são estáticos.|
| Protocolos permitidos | Os protocolos permitidos para um pedido efetuado com a conta SAS. Recomenda-se a definição de HTTPS.|

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre SAS, consulte os artigos seguintes:
- [Utilizar assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Assinaturas de acesso, parte 2 de partilhado: Criar e utilizar um SAS com armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Para obter mais informações sobre como configurar a autenticação de token, consulte [recursos de proteger conteúdo rede de entrega Azure com a autenticação de token](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
