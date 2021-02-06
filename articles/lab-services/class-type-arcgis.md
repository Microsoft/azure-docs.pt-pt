---
title: Crie um laboratório para o ArcMap\ArcGIS Desktop com a Azure Lab Services | Microsoft Docs
description: Aprenda a criar um laboratório para aulas usando o ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: dbe4191b64773b71bc1ae04842d824fbfead8e55
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628170"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Criar um laboratório para o ArcMap\ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) é um tipo de sistema de informação geográfica (SIG).  O ArcGIS é utilizado para fazer mapas e trabalhar com dados geográficos fornecidos pelo [Instituto de Investigação de Sistemas Ambientais](https://www.esri.com/en-us/home) (ESRI).  Embora o ArcGIS Desktop inclua várias aplicações, este artigo mostra como configurar laboratórios para a utilização do ArcMap.  [O ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) é usado para fazer, editar e analisar mapas 2D.

## <a name="lab-configuration"></a>Configuração de laboratório

Para começar a criar um laboratório para usar o ArcMap, precisa de uma assinatura Azure e uma conta de laboratório.  Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Depois de obter uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab.  Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [configurar uma conta de laboratório.](tutorial-setup-lab-account.md)  Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Definições de conta de laboratório

Ative as definições da sua conta de laboratório como descrito na tabela seguinte.  Para obter mais informações sobre como ativar as imagens do Azure Marketplace, consulte [especificar as imagens do Azure Marketplace disponíveis para os criadores de laboratório.](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images)

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Ative a imagem do Windows 10 Pro ou Do Windows 10 Pro N para utilização na sua conta de laboratório.|

### <a name="licensing-server"></a>Servidor de licenciamento

Um tipo de licenciamento que o ArcGIS Desktop oferece são [licenças de utilização simultâneas.](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm)  Isto requer que instale o Gestor de Licenças ArcGIS no seu servidor de licença.  O Gestor de Licenças regista o número de cópias de software que podem ser executadas ao mesmo tempo.  Para obter mais informações sobre como configurar o Gestor de Licenças no seu servidor, consulte o [Guia do Gestor de Licenças](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

O servidor de licenças está tipicamente localizado na sua rede no local ou alojado numa máquina virtual Azure dentro de uma rede virtual Azure.  Depois de configurar o servidor de licença, terá de [espreitar a rede virtual](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network) com a sua [conta de laboratório.](https://docs.microsoft.com/azure/lab-services/tutorial-setup-lab-account)  Tens de fazer o olhar da rede antes de criares o laboratório para que os teus VMs de laboratório possam aceder ao servidor da licença e vice-versa.

Para obter mais informações, consulte [Configurar um servidor de licença como um recurso partilhado.](how-to-create-a-lab-with-shared-resource.md)

### <a name="lab-settings"></a>Configurações de laboratório

O tamanho da máquina virtual (VM) que recomendamos usar para o ArcGIS Desktop depende das aplicações, extensões e versões específicas que os alunos irão utilizar.  O tamanho do VM também depende das cargas de trabalho que os alunos devem realizar.  Consulte os [requisitos do sistema de ambiente de trabalho ArcGIS](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) para ajudar a identificar o tamanho VM.  Uma vez identificado o tamanho potencial de VM, recomendamos que teste as cargas de trabalho dos seus alunos para garantir um desempenho adequado.

Neste artigo, recomendamos a utilização do tamanho [ **VM médio**](administrator-guide.md#vm-sizing) para a versão [10.7.1 do ArcMap,](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm)assumindo que não são utilizadas outras extensões de Ambiente de Trabalho ArcGIS.  No entanto, dependendo das necessidades da sua classe, poderá necessitar de um tamanho VM **grande** ou mesmo **médio(visualização).**  Por exemplo, a [extensão do Analista Espacial](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) que está incluída no ArcGIS Desktop suporta uma GPU para um desempenho melhorado, mas não requer a utilização de uma GPU.

| Definição de laboratório | Valor e descrição |
| ------------ | ------------------ |
|Tamanho da máquina virtual| **Médio.**  Mais adequado para bases de dados relacionais, caching na memória e análise.|  

### <a name="template-machine"></a>Máquina de modelo

Os passos desta secção mostram como configurar o modelo VM:

1.  Inicie o VM do modelo e ligue-se à máquina utilizando RDP.

2.  Faça o download e instale os componentes do Ambiente de Trabalho ArcGIS utilizando instruções do ESRI.  Estas etapas incluem a atribuição do gestor de licenças para o licenciamento de uso simultâneo: 
    - [Introdução à instalação e configuração do Ambiente de Trabalho arcgis](https://desktop.arcgis.com/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Configurar um armazenamento externo de backup para os estudantes.  Os alunos podem guardar ficheiros diretamente para o VM atribuído, uma vez que todas as alterações que fazem são guardadas em todas as sessões.  No entanto, recomendamos que os alunos apoiem o seu trabalho de armazenamento que seja externo ao seu VM por algumas razões:
    - Para permitir que os alunos acedam ao seu trabalho após o fim da aula e do laboratório.  
    - No caso de o aluno entrar em mau estado e a sua imagem necessitar de ser [reposta.](how-to-set-virtual-machine-passwords.md#reset-vms)

    Com o ArcGIS, cada aluno deve fazer o back up dos seguintes ficheiros no final de cada sessão de trabalho:

    - ficheiro mxd, que armazena a informação de layout para um projeto.
    - Bases de arquivos geoduqueram todos os dados produzidos pela ArcGIS.
    - Quaisquer outros dados que o aluno possa estar a usar, tais como ficheiros raster, ficheiros de forma, GeoTIFF, etc.

    Recomendamos a utilização do OneDrive para armazenamento de cópias de segurança.  Para configurar o OneDrive no modelo VM, siga os passos do artigo [Instale e configuure o OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  Finalmente, [publique](how-to-create-manage-template.md#publish-the-template-vm) o modelo VM para criar o VM dos alunos.

### <a name="auto-shutdown-and-disconnect-settings"></a>Definições de desconexão e desconexão automáticas

As [definições de desconexão e desconexão automática de](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) um laboratório ajudam a garantir que o VM de um aluno é desligado quando não está a ser usado.  Estas definições devem ser definidas de acordo com os tipos de cargas de trabalho que os seus alunos irão realizar para que o seu VM não seja desligado no meio do seu trabalho.  Por exemplo, os **utilizadores desligarem quando as máquinas virtuais estão inativas** desliga o aluno da sua sessão de PDR depois de não terem sido detetadas entradas de rato ou teclado durante um determinado período de tempo.  Esta definição deve permitir tempo suficiente para cargas de trabalho em que o aluno não esteja a utilizar ativamente o rato ou o teclado, tais como para executar consultas longas ou esperar pela renderização.

Para o ArcGIS, recomendamos os seguintes valores para estas definições:
- Desligue os utilizadores quando as máquinas virtuais estão inativas
    - 30 minutos após a deteção do estado ocioso
- Desligue as máquinas virtuais quando os utilizadores desligarem
    - 15 minutos após a desconexão do utilizador

## <a name="cost"></a>Custo

Vamos cobrir uma possível estimativa de custos para esta aula. Esta estimativa não inclui o custo de executar o servidor de licença. Usaremos uma turma de 25 alunos. Há 20 horas de horário de aula. Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora do horário de aulas programado. O tamanho da máquina virtual que escolhemos foi **Medium,** que é 42 unidades de laboratório.

25 alunos \* (20 horas programadas + 10 horas de quota) \* 42 Unidades de Laboratório * 0,01 USD por hora = 315,00 USD

>[!IMPORTANT]
> A estimativa de custos é apenas para fins.  Para obter detalhes atuais sobre os preços, consulte [o Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)  

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)