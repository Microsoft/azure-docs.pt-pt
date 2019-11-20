---
title: Carregar um domínio raiz ou Apex para uma porta frontal existente-portal do Azure
description: Saiba como carregar um domínio raiz ou Apex para uma porta frontal existente usando o portal do Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184617"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Carregar um domínio raiz ou Apex na sua porta frontal
A porta frontal do Azure usa registros CNAME para validar a propriedade do domínio para a integração de domínios personalizados. Além disso, a porta frontal não expõe o endereço IP de front-end associado ao seu perfil de porta frontal e, portanto, não é possível mapear seu domínio Apex para um endereço IP, se a intenção for integrar-o à porta frontal do Azure.

O protocolo DNS impede a atribuição de registros CNAME no Apex da zona. Por exemplo, se seu domínio for `contoso.com`; Você pode criar registros CNAME para `somelabel.contoso.com`; Mas você não pode criar CNAME para `contoso.com` si mesmo. Essa restrição apresenta um problema para proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás da porta frontal do Azure. Como o uso de um perfil de porta frontal requer a criação de um registro CNAME, não é possível apontar para o perfil de porta frontal do Apex da zona.

Esse problema é resolvido usando registros de alias no DNS do Azure. Ao contrário dos registros CNAME, os registros de alias são criados no Apex da zona e os proprietários do aplicativo podem usá-lo para apontar o registro de Apex da zona para um perfil de porta frontal que tenha pontos de extremidade públicos. Os proprietários de aplicativo apontam para o mesmo perfil de porta frontal usado para qualquer outro domínio dentro de sua zona DNS. Por exemplo, `contoso.com` e `www.contoso.com` podem apontar para o mesmo perfil de porta frontal. 

O mapeamento de seu Apex ou domínio raiz para o perfil de porta frontal basicamente requer o nivelamento de CNAME ou a caça de DNS, que é um mecanismo no qual o provedor DNS resolve recursivamente a entrada CNAME até atingir um endereço IP. Essa funcionalidade é suportada pelo DNS do Azure para pontos de extremidade de porta frontal. 

> [!NOTE]
> Também há outros provedores DNS que dão suporte ao nivelamento CNAME ou à procura de DNS. no entanto, a porta frontal do Azure recomenda usar o DNS do Azure para seus clientes para hospedar seus domínios.

Você pode usar a portal do Azure para carregar um domínio Apex na sua porta frontal e habilitar o HTTPS nele associando-o a um certificado para terminação SSL. Os domínios Apex também são chamados de domínios raiz ou Naked.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um registro de alias que aponte para o perfil de porta frontal
> * Adicionar o domínio raiz à porta frontal
> * Configurar HTTPS no domínio raiz

> [!NOTE]
> Este tutorial requer que você já tenha um perfil de porta de front-end criado. Consulte outros tutoriais como [o início rápido: criar uma porta frontal](./quickstart-create-front-door.md) ou [criar uma porta frontal com o redirecionamento de http para https](./front-door-how-to-redirect-https.md) para começar.

## <a name="create-an-alias-record-for-zone-apex"></a>Criar um registro de alias para o Apex da zona

1. Abra a configuração de **DNS do Azure** para o domínio a ser integrado.
2. Crie ou edite o registro do Apex da zona.
3. Selecione o **tipo** de registro como _um_ registro e, em seguida, selecione _Sim_ para conjunto de **registros de alias**. O **tipo de alias** deve ser definido como _recurso do Azure_.
4. Escolha a assinatura do Azure em que o perfil de porta frontal está hospedado e, em seguida, selecione o recurso de porta frontal na lista suspensa de **recursos do Azure** .
5. Clique em **OK** para enviar suas alterações.

    ![Registro de alias para o Apex da zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. A etapa acima criará um registro de Apex de zona apontando para o recurso de porta frontal e também um mapeamento de registro CNAME ' afdverify ' (exemplo-`afdverify.contosonews.com`) para `afdverify.<name>.azurefd.net` que será usado para integração do domínio em seu perfil de porta frontal.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Carregar o domínio personalizado na sua porta frontal

1. Na guia Designer de porta frontal, clique no ícone "+" na seção hosts de front-end para adicionar um novo domínio personalizado.
2. Insira o nome de domínio raiz ou Apex no campo nome de host personalizado, `contosonews.com`de exemplo.
3. Depois que o mapeamento CNAME do domínio para sua porta frontal for validado, clique em **Adicionar** para adicionar o domínio personalizado.
4. Clique em **salvar** para enviar as alterações.

![Menu de domínio personalizado](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Habilitar HTTPS em seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e sob a seção **domínio personalizado https**, altere o status para **habilitado**.
2. Selecione o **tipo de gerenciamento de certificado** para _' usar meu próprio certificado '_ .

> [!WARNING]
> No momento, não há suporte para o tipo de gerenciamento de certificado gerenciado de porta frontal para Apex ou domínios raiz. A única opção disponível para habilitar HTTPS em um Apex ou domínio raiz para a porta frontal é usar seu próprio certificado SSL personalizado hospedado no Azure Key Vault.

3. Verifique se você configurou as permissões corretas para a porta frontal acessar o cofre de chaves, conforme observado na interface do usuário, antes de prosseguir para a próxima etapa.
4. Escolha uma **conta de Key Vault** da sua assinatura atual e, em seguida, selecione a **versão** **secreta** e secreta apropriada para mapear para o certificado correto.
5. Clique em **Atualizar** para salvar a seleção e, em seguida, clique em **salvar**.
6. Clique em **Atualizar** após alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso do provisionamento de certificado. 

> [!WARNING]
> Verifique se você criou as regras de roteamento apropriadas para seu domínio Apex ou adicionou o domínio às regras de roteamento existentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).