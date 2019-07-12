---
title: Carregar um domínio de raiz ou apex para uma porta da frente existente com o portal do Azure
description: Saiba como integrar um domínio de raiz ou apex para uma porta da frente existente com o portal do Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 8fe8da95a61d2f2bb35095236131670cb6ef0e70
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605782"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Carregar um domínio de raiz ou apex na sua porta de entrada
Porta de entrada do Azure utiliza registos CNAME para validar a propriedade do domínio para a integração de domínios personalizados. Além disso, a porta de entrada não expõe o endereço IP de front-end associado a seu perfil de porta de entrada e por isso, não é possível mapear o domínio de vértice para um endereço IP, se a intenção é integrá-lo a porta da frente do Azure.

O protocolo DNS impede a atribuição de registos CNAME no vértice da zona. Por exemplo, se o seu domínio for `contoso.com`; pode criar registos CNAME para `somelabel.contoso.com`; mas não é possível criar o CNAME para `contoso.com` em si. Esta restrição apresenta um problema para os proprietários da aplicação que tenham aplicações com balanceamento de carga por trás de porta da frente do Azure. Uma vez que a utilizar um perfil de porta de entrada requer a criação de um registo CNAME, não é possível apontar para o perfil de porta de entrada do vértice da zona.

Esse problema foi resolvido com registos de alias no DNS do Azure. Ao contrário de registos CNAME, os registos de alias são criados no vértice da zona e proprietários de aplicativos podem utilizá-lo para apontar o respetivo registo de apex de zona para um perfil de porta de entrada que tem pontos finais públicos. Os proprietários dos aplicativos de apontar para o mesmo perfil de porta de entrada que é utilizado para qualquer outro domínio na sua zona DNS. Por exemplo, `contoso.com` e `www.contoso.com` pode apontar para o mesmo perfil de porta de entrada. 

Mapeamento de seu domínio de raiz ou apex ao seu perfil de porta de entrada basicamente requer CNAME mesclar ou DNS perseguindo, que é um mecanismo de onde no DNS fornecedor recursivamente resolve a entrada CNAME até atingir um endereço IP. Esta funcionalidade é suportada pelo DNS do Azure para pontos finais de porta de entrada. 

> [!NOTE]
> Existem outros fornecedores DNS também que suportam o CNAME mesclar ou DNS perseguindo, no entanto, porta de entrada do Azure recomenda a utilização de DNS do Azure para seus clientes para o alojamento dos seus domínios.

Pode utilizar o portal do Azure para carregar um domínio de vértice no sua porta de entrada e ativar o HTTPS no mesmo, associando-o com um certificado para a terminação de SSL. Domínios de Apex também são referidos como raiz ou a domínios sem "www".

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um registo de alias que aponte para o seu perfil de porta de entrada
> * Adicionar o domínio de raiz para a porta de entrada
> * Configurar HTTPS num domínio de raiz

> [!NOTE]
> Este tutorial requer que já tem um perfil de porta de entrada criado. Consulte os outros tutoriais como [início rápido: Criar uma porta de entrada](./quickstart-create-front-door.md) ou [criar uma porta de entrada com HTTP para redirecionamento a HTTPS](./front-door-how-to-redirect-https.md) para começar a utilizar.

## <a name="create-an-alias-record-for-zone-apex"></a>Criar um registo de alias para apex de zona

1. Open **DNS do Azure** configuração para o domínio a carregar.
2. Criar ou editar o registo de apex de zona.
3. Selecione o registo **tipo** como _uma_ gravar e, em seguida, selecione _Sim_ para **conjunto de registos de Alias**. **Tipo de alias** deve ser definido como _recursos do Azure_.
4. Escolha a subscrição do Azure onde está alojado o seu perfil de porta de entrada e, em seguida, selecione o recurso de porta de entrada do **recursos do Azure** lista pendente.
5. Clique em **OK** para submeter as alterações.

    ![Registo de alias para apex de zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. O passo acima irá criar um registo de apex de zona que aponte para o recurso de porta de entrada e também um mapeamento de registos CNAME 'afdverify' (exemplo: `afdverify.contosonews.com`) para `afdverify.<name>.azurefd.net` que será utilizado para a integração do domínio no seu perfil de porta de entrada.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Carregar o domínio personalizado em sua porta de entrada

1. No separador de designer da porta de entrada, clique em "+" secção de ícone nos anfitriões de front-end para adicionar um novo domínio personalizado.
2. Introduza o nome de domínio de raiz ou de vértice no campo de nome de anfitrião personalizado, exemplo `contosonews.com`.
3. Depois do mapeamento CNAME do domínio para a porta de entrada é validado, clique em **adicionar** para adicionar o domínio personalizado.
4. Clique em **guardar** para submeter as alterações.

![Menu de domínio personalizado](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Ativar o HTTPS no seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e na secção **HTTPS de domínio personalizado**, altere o estado para **ativado**.
2. Selecione o **tipo de gestão de certificado** ao _'Utilizar o meu próprio certificado'_ .

> [!WARNING]
> Tipo de gestão de certificados de porta gerida front-não é atualmente suportado para domínios de raiz ou de vértice. A única opção disponível para ativar o HTTPS num domínio raiz ou apex para a porta de entrada está a utilizar o seu próprio certificado SSL personalizado alojado no Azure Key Vault.

3. Certifique-se de que tiver configurado as permissões corretas para a porta de entrada aceder a sua chave de cofre, conforme observado na interface do Usuário, antes de prosseguir para o passo seguinte.
4. Escolher uma **conta do Key Vault** da sua subscrição atual e em seguida, selecione o adequado **segredo** e **versão do segredo** para mapear para o certificado correto.
5. Clique em **atualização** para guardar a seleção e, em seguida, clique em **guardar**.
6. Clique em **atualizar** após alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso do aprovisionamento de certificados. 

> [!WARNING]
> Certifique-se de que tem de criar regras de encaminhamento adequadas para o seu domínio de vértice ou adicionou o domínio para as regras de roteamento existentes.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).