---
title: Configurar ligações seguras de cluster de Azure Service Fabric | Documentos da Microsoft
description: Saiba como utilizar o Visual Studio para configurar ligações seguras suportadas pelo cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 8d76a2144234591792359ed8dd4a0779e6a2fc5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60628301"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configurar ligações seguras a um cluster do Service Fabric a partir do Visual Studio
Saiba como utilizar o Visual Studio para aceder de forma segura um cluster do Azure Service Fabric com as políticas de controlo de acesso configuradas.

## <a name="cluster-connection-types"></a>Tipos de ligação do cluster
Dois tipos de ligações são suportados pelo cluster do Azure Service Fabric: **não segura** ligações e **x509 baseada em certificado** proteger ligações. (Para clusters do Service Fabric alojado no local, **Windows** e **dSTS** autenticações também são suportadas.) Tem de configurar o tipo de ligação do cluster quando o cluster está a ser criado. Depois de criado, não é possível alterar o tipo de ligação.

As ferramentas do Visual Studio Service Fabric suportam todos os tipos de autenticação para ligar a um cluster para publicação. Ver [como configurar um cluster do Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md) para obter instruções sobre como configurar um cluster do Service Fabric seguro.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configurar ligações de cluster em perfis de publicação.
Se publicar um projeto do Service Fabric a partir do Visual Studio, utilize o **publicar a aplicação do Service Fabric** caixa de diálogo para escolher um cluster do Azure Service Fabric. Sob **ponto final de ligação**, selecione um cluster existente na sua subscrição.

![O * * caixa de diálogo Publicar Service Fabric Application * * é usada para configurar uma ligação do Service Fabric.][publishdialog]

O **publicar a aplicação do Service Fabric** caixa de diálogo valida automaticamente a ligação de cluster. Se lhe for pedido, inicie sessão na sua conta do Azure. Se a validação é efetuada, significa que o sistema tem os certificados corretos instalados para estabelecer ligação ao cluster em segurança ou o cluster estiver não segura. Falhas de validação podem ser causadas por problemas de rede ou por não ter o seu sistema corretamente configurado para ligar a um cluster seguro.

![O * * publicar Service Fabric Application * * caixa de diálogo valida um existente, corretamente configurado ligação de cluster do Service Fabric.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Para ligar a um cluster seguro
1. Certifique-se de que poder aceder a um dos certificados de cliente que o cluster de destino confie. O certificado é geralmente partilhado como um ficheiro Personal Information Exchange (. pfx). Ver [como configurar um cluster do Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md) para saber como configurar o servidor para conceder acesso a um cliente.
2. Instale o certificado fidedigno. Para tal, faça duplo clique no ficheiro. pfx ou utilize o script do PowerShell Import-PfxCertificate para importar os certificados. Instalar o certificado para **Cert: \LocalMachine\My**. Há problemas em aceitar todas as configurações padrão ao importar o certificado.
3. Escolha o **publicar...**  comando no menu de atalho do projeto para abrir o **publicar a aplicação do Azure** caixa de diálogo e, em seguida, selecione o cluster de destino. A ferramenta automaticamente resolve a ligação e guarda os parâmetros de ligação segura no perfil de publicação.
4. Opcional: Pode editar o perfil de publicação para especificar uma ligação de cluster seguro.
   
   Uma vez que estiver a editar manualmente o ficheiro XML do perfil de publicação para especificar as informações do certificado, certifique-se de que tenha em atenção o nome de arquivo de certificados, armazene a localização e o thumbprint do certificado. Terá de fornecer estes valores para o arquivo do certificado, atribua um nome e localização do arquivo. Consulte [como: Obtenha o Thumbprint de um certificado](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) para obter mais informações.
   
   Pode utilizar o *ClusterConnectionParameters* parâmetros para especificar os parâmetros do PowerShell para utilizar quando ligar ao cluster do Service Fabric. Parâmetros válidos são qualquer um que serão aceites pelo cmdlet Connect-ServiceFabricCluster. Ver [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) para obter uma lista de parâmetros.
   
   Se está publicando num cluster remoto, tem de especificar os parâmetros adequados para esse cluster específico. Segue-se um exemplo de ligação a um cluster não segura:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Eis um exemplo para ligar a uma x509 cluster seguro com base em certificado:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Editar quaisquer outras definições necessárias, tais como parâmetros de atualização e a localização do ficheiro de parâmetro de aplicação e, em seguida, publicar a aplicação a partir da **publicar a aplicação do Service Fabric** caixa de diálogo no Visual Studio.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como aceder aos clusters do Service Fabric, veja [visualizar o seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
