---
title: Usando a CDN do Azure com SAS | Microsoft Docs
description: A CDN do Azure dá suporte ao uso de SAS (assinatura de acesso compartilhado) para conceder acesso limitado a contêineres de armazenamento privado.
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
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982421"
---
# <a name="using-azure-cdn-with-sas"></a>Usando a CDN do Azure com SAS

Quando você configura uma conta de armazenamento para a CDN (rede de distribuição de conteúdo) do Azure para usar para armazenar em cache o conteúdo, por padrão, qualquer pessoa que conheça as URLs de seus contêineres de armazenamento pode acessar os arquivos que você carregou. Para proteger os arquivos em sua conta de armazenamento, você pode definir o acesso de seus contêineres de armazenamento de público para privado. No entanto, se você fizer isso, ninguém poderá acessar seus arquivos. 

Se quiser conceder acesso limitado aos contentores de armazenamento privado, pode utilizar a funcionalidade Assinatura de Acesso Partilhado (SAS) na sua conta de armazenamento do Azure. Uma SAS é um URI que concede direitos de acesso restritos aos recursos de Armazenamento do Azure sem expor a sua chave de conta. Você pode fornecer uma SAS para clientes em que você não confia com sua chave de conta de armazenamento, mas para quem você deseja delegar acesso a determinados recursos de conta de armazenamento. Ao distribuir um URI de assinatura de acesso compartilhado para esses clientes, você concede a eles acesso a um recurso por um período de tempo especificado.
 
Com uma SAS, você pode definir vários parâmetros de acesso a um blob, como horários de início e de expiração, permissões (leitura/gravação) e intervalos de IP. Este artigo descreve como usar SAS em conjunto com a CDN do Azure. Para obter mais informações sobre SAS, incluindo como criá-la e suas opções de parâmetro, consulte [usando SAS (assinaturas de acesso compartilhado)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configurando a CDN do Azure para trabalhar com SAS de armazenamento
As três opções a seguir são recomendadas para o uso de SAS com a CDN do Azure. Todas as opções pressupõem que você já criou uma SAS de trabalho (consulte pré-requisitos). 
 
### <a name="prerequisites"></a>Pré-requisitos
Para iniciar, crie uma conta de armazenamento e, em seguida, gere uma SAS para seu ativo. Você pode gerar dois tipos de assinaturas de acesso armazenadas: uma SAS de serviço ou uma SAS de conta. Para obter mais informações, consulte [tipos de assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Depois de gerar um token SAS, você pode acessar o arquivo de armazenamento de BLOBs anexando `?sv=<SAS token>` -o à sua URL. Essa URL tem o seguinte formato: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Por exemplo:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para obter mais informações sobre como definir parâmetros, consulte [considerações de parâmetro SAS](#sas-parameter-considerations) e [parâmetros de assinatura de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).

![Configurações de SAS da CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opção 1: Usando SAS com passagem para o armazenamento de BLOBs da CDN do Azure

Essa opção é a mais simples e usa um único token SAS, que é passado da CDN do Azure para o servidor de origem.
 
1. Selecione um ponto de extremidade, selecione **regras de cache**e, em seguida, selecione **armazenar em cache cada URL exclusiva** na lista **cache de cadeia de caracteres de consulta** .

    ![Regras de cache da CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Depois de configurar a SAS em sua conta de armazenamento, você deve usar o token SAS com o ponto de extremidade da CDN e as URLs do servidor de origem para acessar o arquivo. 
   
   A URL resultante do ponto de extremidade da CDN tem o seguinte formato:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Por exemplo:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ajuste a duração do cache usando regras de cache ou adicionando `Cache-Control` cabeçalhos no servidor de origem. Como a CDN do Azure trata o token SAS como uma cadeia de caracteres de consulta simples, como uma prática recomendada, você deve configurar uma duração de cache que expira em ou antes do tempo de expiração da SAS. Caso contrário, se um arquivo for armazenado em cache por uma duração maior do que a SAS estiver ativa, o arquivo poderá ser acessado do servidor de origem da CDN do Azure após o tempo de expiração da SAS ter decorrido. Se essa situação ocorrer e você quiser tornar o arquivo armazenado em cache inacessível, você deverá executar uma operação de limpeza no arquivo para limpá-lo do cache. Para obter informações sobre como definir a duração do cache na CDN do Azure, consulte [controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opção 2: Token SAS da CDN oculta usando uma regra de regravação
 
Essa opção está disponível somente para perfis **da CDN Premium do Azure da Verizon** . Com essa opção, você pode proteger o armazenamento de BLOBs no servidor de origem. Talvez você queira usar essa opção se não precisar de restrições de acesso específicas para o arquivo, mas quiser impedir que os usuários acessem a origem de armazenamento diretamente para melhorar os tempos de descarregamento da CDN do Azure. O token SAS, que é desconhecido para o usuário, é necessário para qualquer pessoa que acessa arquivos no contêiner especificado do servidor de origem. No entanto, devido à regra de reescrita de URL, o token SAS não é necessário no ponto de extremidade da CDN.
 
1. Use o [mecanismo de regras](cdn-rules-engine.md) para criar uma regra de reescrita de URL. Novas regras levam até 4 horas para serem propagadas.

   ![Botão gerenciar CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Botão do mecanismo de regras da CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   A regra de reescrita de URL de exemplo a seguir usa um padrão de expressão regular com um grupo de captura e um ponto de extremidade chamado *sasstoragedemo*:
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de regravação de URL da](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   CDN-regra de regravação da URL CDN restante![-direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Depois que a nova regra se torna ativa, qualquer pessoa pode acessar arquivos no contêiner especificado no ponto de extremidade da CDN, independentemente de estar usando um token SAS na URL. Este é o formato:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Por exemplo:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Ajuste a duração do cache usando regras de cache ou adicionando `Cache-Control` cabeçalhos no servidor de origem. Como a CDN do Azure trata o token SAS como uma cadeia de caracteres de consulta simples, como uma prática recomendada, você deve configurar uma duração de cache que expira em ou antes do tempo de expiração da SAS. Caso contrário, se um arquivo for armazenado em cache por uma duração maior do que a SAS estiver ativa, o arquivo poderá ser acessado do servidor de origem da CDN do Azure após o tempo de expiração da SAS ter decorrido. Se essa situação ocorrer e você quiser tornar o arquivo armazenado em cache inacessível, você deverá executar uma operação de limpeza no arquivo para limpá-lo do cache. Para obter informações sobre como definir a duração do cache na CDN do Azure, consulte [controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opção 3: Usando a autenticação de token de segurança da CDN com uma regra de regravação

Para usar a autenticação de token de segurança da CDN do Azure, você deve ter um perfil **da CDN Premium do Azure da Verizon** . Essa opção é a mais segura e personalizável. O acesso do cliente é baseado nos parâmetros de segurança que você define no token de segurança. Depois de criar e configurar o token de segurança, ele será necessário em todas as URLs de ponto de extremidade da CDN. No entanto, devido à regra de reescrita de URL, o token SAS não é necessário no ponto de extremidade da CDN. Se o token SAS mais tarde se tornar inválido, a CDN do Azure não será mais capaz de revalidar o conteúdo do servidor de origem.

1. [Crie um token de segurança da CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) e ative-o usando o mecanismo de regras para o ponto de extremidade e o caminho da CDN em que os usuários podem acessar o arquivo.

   Uma URL de ponto de extremidade de token de segurança tem o seguinte formato:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Por exemplo:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   As opções de parâmetro para uma autenticação de token de segurança são diferentes das opções de parâmetro para um token SAS. Se você optar por usar um tempo de expiração ao criar um token de segurança, defina-o com o mesmo valor que o tempo de expiração para o token SAS. Isso garante que o tempo de expiração seja previsível. 
 
2. Use o [mecanismo de regras](cdn-rules-engine.md) para criar uma regra de reescrita de URL para habilitar o acesso de token SAS a todos os BLOBs no contêiner. Novas regras levam até 4 horas para serem propagadas.

   A regra de reescrita de URL de exemplo a seguir usa um padrão de expressão regular com um grupo de captura e um ponto de extremidade chamado *sasstoragedemo*:
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de regravação de URL da](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   CDN-regra de regravação da URL CDN restante![-direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Se você renovar a SAS, certifique-se de atualizar a regra de regravação de URL com o novo token SAS. 

## <a name="sas-parameter-considerations"></a>Considerações sobre parâmetros SAS

Como os parâmetros SAS não são visíveis para a CDN do Azure, a CDN do Azure não pode alterar seu comportamento de entrega com base neles. As restrições de parâmetro definidas aplicam-se somente às solicitações que a CDN do Azure faz para o servidor de origem, não para solicitações do cliente para a CDN do Azure. Essa distinção é importante considerar quando você define os parâmetros de SAS. Se esses recursos avançados forem necessários e você estiver usando a [opção 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), defina as restrições apropriadas no token de segurança da CDN do Azure.

| Nome do parâmetro SAS | Descrição |
| --- | --- |
| Start | A hora em que a CDN do Azure pode começar a acessar o arquivo de BLOB. Devido à distorção do relógio (quando um sinal de relógio chega em momentos diferentes para diferentes componentes), escolha um tempo de 15 minutos antes se desejar que o ativo esteja disponível imediatamente. |
| fim | O tempo após o qual a CDN do Azure não pode mais acessar o arquivo de BLOB. Os arquivos armazenados em cache anteriormente na CDN do Azure ainda estão acessíveis. Para controlar a hora de expiração do arquivo, defina a hora de expiração apropriada no token de segurança da CDN do Azure ou limpe o ativo. |
| Endereços IP permitidos | Opcional. Se você estiver usando a **CDN do Azure da Verizon**, poderá definir esse parâmetro para os intervalos definidos nos [intervalos de IP do servidor de borda CDN do Azure da Verizon](/azure/cdn/cdn-pop-list-api). Se você estiver usando a **CDN do Azure do Akamai**, não será possível definir o parâmetro de intervalos de IP porque os endereços IP não são estáticos.|
| Protocolos permitidos | Os protocolos permitidos para uma solicitação feita com a SAS da conta. A configuração de HTTPS é recomendada.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre SAS, consulte os seguintes artigos:
- [Utilizar assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Assinaturas de acesso compartilhado, parte 2: Criar e usar uma SAS com o armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Para obter mais informações sobre como configurar a autenticação de token, consulte [protegendo ativos de rede de distribuição de conteúdo do Azure com autenticação de token](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
