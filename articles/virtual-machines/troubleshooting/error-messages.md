---
title: Códigos de erro VM comuns em Azure Microsoft Docs
description: Compreenda alguns dos códigos de erro comuns encontrados quando você fornecer e gerir máquinas virtuais em Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543023"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Compreender as mensagens de erro comuns na gestão de máquinas virtuais no Azure

Este artigo descreve alguns dos códigos de erro mais comuns e mensagens que pode encontrar quando cria ou gere máquinas virtuais (VMs) em Azure.

>[!NOTE]
> Pode deixar comentários nesta página para obter feedback ou através do [feedback do Azure](https://feedback.azure.com/forums/216843-virtual-machines) com #azerrormessage tag.

## <a name="error-response-format"></a>Formato de resposta a erros 
Os VMs Azure utilizam o seguinte formato JSON para resposta a erros:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Uma resposta de erro inclui sempre um código de estado e um objeto de erro. Cada objeto de erro contém sempre um código de erro e uma mensagem. Se o VM for criado com um modelo, o objeto de erro também contém uma secção de detalhes que contém um nível interno de códigos de erro e mensagem. Normalmente, o nível mais interno de mensagem de erro é a falha de raiz.


## <a name="common-virtual-machine-management-errors"></a>Erros comuns de gestão de máquinas virtuais

Esta secção lista as mensagens de erro comuns que pode encontrar na gestão de VMs:

|  Código de Erro  |  Mensagem de Erro  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Não conseguiu adquirir o{0}arrendamento enquanto criava disco ' usando blob com URI {1}. Blob já está em uso.  |  
|  Atribuição Falhada  |  A atribuição falhou. Por favor, tente reduzir o tamanho de VM ou o número de VMs, retentar mais tarde, ou tente implementar para um conjunto de disponibilidade diferente ou localização azure diferente.  |  
|  Atribuição Falhada  |  A atribuição de VM falhou devido a um erro interno. Por favor, tente mais tarde ou tente implantar para um local diferente.  |
|  ArtefactoNão Encontrado  |  A extensão VM{0}com a{1}editora ' e o{2}tipo ' ' não foi encontrada no local '.  |
|  ArtefactoNão Encontrado  |  A extensão{0}com a{1}versão ', tipo{2}' e tipo de manipulador ' da editora ' não pôde ser encontrada no repositório de extensão.  |
|  ArtefactoVersãoNão Encontrada  |  Nenhuma versão encontrada no repositório de artefactos{0}que satisfaça a versão solicitada'  |
|  ArtefactoVersãoNão Encontrada  |  Nenhuma versão encontrada no repositório de artefactos{0}que satisfaça a{1}versão solicitada{2}' para extensão VM com a editora ' e o tipo ' ' ' .  |
|  Disco apegado enquanto está a ser destacado  |  Não é possível anexar o disco de dados{0}' ' ao VM '{1}porque o disco está atualmente a ser desconectado. Por favor, aguarde até que o disco esteja completamente desconectado e tente novamente.  |
|  BadRequest  |  Conjuntos de disponibilidade alinhados ainda não são suportados nesta região.  |
|  BadRequest  |  A adição de um VM com discos geridos para conjunto de disponibilidade não gerido ou adição de um VM com discos à base de blob para gerir o Conjunto de Disponibilidade não é suportado. Por favor, crie um Conjunto de Disponibilidade com um conjunto de propriedades 'gerido' para adicionar um VM com discos geridos.  |
|  BadRequest  |  Os Discos Geridos não são suportados nesta região.  |
|  BadRequest  |  Múltiplas Extensões VM por manipulador não{0}suportadas para o tipo OS '. VMExtension{1}' com{2}manipulador ' ' já adicionado ou especificado na entrada.  |
|  BadRequest  |  A{0}operação ' ' não{1}é suportada em Recursos ' com discos geridos.  |
|  CertificadoImprópriamente Formato  |  A representação JSON do {0} segredo recuperada tem um campo de dados que não é um ficheiro PFX devidamente formatado, ou a palavra-passe fornecida não descodifica corretamente o ficheiro PFX.  |
|  CertificadoImprópriamente Formato  |  Os dados recolhidos não {0} são desserializáveis para a JSON.  |
|  Conflito  |  A redimensionamento do disco só é permitida quando se cria um VM ou quando o VM é deallo.  |
|  UserInput conflituoso  |  {0}O disco ' ' não pode ser fixado,{1}uma vez que o disco já é propriedade da VM '.  |
|  UserInput conflituoso  |  Os grupos de recursos de origem e destino são os mesmos.  |
|  UserInput conflituoso  |  As contas de armazenamento {0} de origem e destino para o disco são diferentes.  |
|  ContainerAlreadyOnLease  |  Já existe um arrendamento no contentor de armazenamento {0}que segura a bolha com URI .  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  O pedido de recursos Move contém recursos KeyVault {0}que são referenciados por um ou mais s no pedido. Isto não é suportado atualmente no Cross subscription Move. Verifique os detalhes de erro dos ids do recurso KeyVault.  |
|  DiagnósticoOperationInternalError  |  Ocorreu um erro interno durante o {0}processamento do perfil de diagnóstico da VM .  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blob {0} já está em uso por outro{1}disco pertencente à VM ' ' ' Pode examinar os metadados blob para obter informações de referência do disco.  |
|  DiskBlobNotFound  |  Incapaz de encontrar blob VHD com URI {0} para disco{1}' ' ' ' '  |
|  DiskBlobNotFound  |  Incapaz de encontrar bolhas VHD {0}com URI .  |
|  DiskCryptonKeySecretMissingTags  |  {0}segredo não tem {1} as etiquetas. Por favor, atualize a versão secreta, adicione as etiquetas necessárias e retente novamente.  |
|  DiskCryptonKeySecretUnwrapFailed  |  Desembrulhar {0} o valor {1} secreto usando a chave falhou.  |
|  DiskImageNotReady  |  A {0} imagem {1} do disco está no estado. Por favor, tente novamente quando a imagem estiver pronta.  |
|  Erro de preparação do disco  |  Um ou mais erros ocorreram durante a preparação dos discos VM. Consulte a vista da instância do disco para obter detalhes.  |
|  Erro de processamento de disco  |  O processamento do disco parou à medida que o VM tem outros discos em discos falhados.  |
|  ImageBlobNotFound  |  Incapaz de encontrar blob VHD com URI {0} para disco{1}' ' ' ' '  |
|  ImageBlobNotFound  |  Incapaz de encontrar bolhas VHD {0}com URI .  |
|  IncorrectDiskBlobType  |  As bolhas de disco só podem ser de tipo de página blob. Blob {0} para{1}disco ' é de bloco tipo blob.  |
|  IncorrectDiskBlobType  |  As bolhas de disco só podem ser de tipo de página blob. Blob {0} é de{1}tipo '.  |
|  IncorrectImageBlobType  |  As bolhas de disco só podem ser de tipo de página blob. Blob {0} para{1}disco ' é de bloco tipo blob.  |
|  IncorrectImageBlobType  |  As bolhas de disco só podem ser de tipo de página blob. Blob {0} é de{1}tipo '.  |
|  Erro de Operação Interna  |  Não conseguiu {0}resolver a conta de armazenamento. Certifique-se de que foi criado através do Fornecedor de Recursos de Armazenamento no mesmo local que o recurso computacional.  |
|  Erro de Operação Interna  |  {0}objetivo procurando tarefas falhou.  |
|  Erro de Operação Interna  |  Ocorreu um erro na validação do{0}perfil de rede da VM' ' .  |
|  InvalidAccountType  |  O Número {0} de Contas é inválido.  |
|  Parâmetro inválido  |  O valor do {0} parâmetro é inválido.  |
|  Parâmetro inválido  |  A palavra-passe do Administrador especificada não é permitida.  |
|  Parâmetro inválido  |  "A palavra-passe {0}fornecida{1} deve ser entre {2} - caracteres longos e devem satisfazer pelo menos os requisitos de complexidade da palavra-passe a partir dos seguintes: <ol><li> Contém um personagem maiúsculo</li><li>Contém um caráter minúsculo</li><li>Contém um dígito numérico</li><li>Contém um personagem especial.</li></ol>  |
|  Parâmetro inválido  |  O nome de utilizador do Administrador especificado não é permitido.  |
|  Parâmetro inválido  |  Não é possível anexar um disco OPERATIVO existente se o VM for criado a partir de uma plataforma ou imagem de utilizador.  |
|  Parâmetro inválido  |  O {0} nome do contentor é inválido. Os nomes dos contentores devem ter 3-63 caracteres de comprimento e podem conter apenas caracteres alfanuméricos inferiores e hífen. O hífen deve ser precedido e seguido por um carácter alfanumérico.  |
|  Parâmetro inválido  |  O {0} nome {1} do recipiente no URL é inválido. Os nomes dos contentores devem ter 3-63 caracteres de comprimento e podem conter apenas caracteres alfanuméricos inferiores e hífen. O hífen deve ser precedido e seguido por um carácter alfanumérico.  |
|  Parâmetro inválido  |  O nome blob {0} no URL contém um corte. Isto não é atualmente suportado para discos.  |
|  Parâmetro inválido  |  O {0} URI não parece estar correto blob URI.  |
|  Parâmetro inválido  |  Um disco{0}chamado ' ' já {1}usa o mesmo LUN: .  |
|  Parâmetro inválido  |  Um disco{0}chamado ' ' já existe.  |
|  Parâmetro inválido  |  Não é possível especificar as sobreposições de imagem do utilizador para um disco já definido na referência de imagem especificada.  |
|  Parâmetro inválido  |  Um disco{0}chamado ' ' já usa {1}o mesmo URL VHD .  |
|  Parâmetro inválido  |  A contagem {0} de domínio de avaria {1} {2}especificada deve cair na gama para .  |
|  Parâmetro inválido  |  O tipo {0} de licença é inválido. Os tipos de licença válidos são: Windows_Client ou Windows_Server, sensível ao caso.  |
|  Parâmetro inválido  |  O nome do {0} anfitrião Linux não pode {1}exceder caracteres de comprimento ou conter os seguintes caracteres: .  |
|  Parâmetro inválido  |  A trajetória de destino das chaves públicas Ssh está atualmente limitada ao seu valor {0} padrão devido a uma emissão conhecida no agente de provisionamento Linux.  |
|  Parâmetro inválido  |  Um disco na {0} LUN já existe.  |
|  Parâmetro inválido  |  A {0} subscrição do pedido {1} deve corresponder à subscrição contida no id do disco gerido.  |
|  Parâmetro inválido  |  Os dados personalizados no OSProfile devem estar na codificação base64 e com um comprimento máximo de {0} caracteres.  |
|  Parâmetro inválido  |  O nome blob no {0} {1}URL deve terminar com a extensão ' ' ' ' '  |
|  Parâmetro inválido  |  {0}' não é um prefixo de nome vHD capturado válido. Um prefixo válido corresponde{1}a regex ' ' ' ' '  |
|  Parâmetro inválido  |  Os certificados não podem ser adicionados ao seu VM se o agente VM não for provisionado.  |
|  Parâmetro inválido  |  Um disco na {0} LUN já existe.  |
|  Parâmetro inválido  |  Não é possível criar o VM porque a dimensão {0} solicitada não está disponível no cluster onde o conjunto de disponibilidade está atualmente atribuído. Os tamanhos disponíveis {1}são: . Leia mais sobre a estratégia https://aka.ms/azure-resizevmde redimensionamento vm em .  |
|  Parâmetro inválido  |  O tamanho {0} de VM solicitado não está disponível na região atual. Os tamanhos disponíveis na região atual são: {1}. Saiba mais sobre os tamanhos vm https://aka.ms/azure-regionsdisponíveis em cada região em .  |
|  Parâmetro inválido  |  O tamanho {0} de VM solicitado não está disponível na região atual. Saiba mais sobre os tamanhos vm https://aka.ms/azure-regionsdisponíveis em cada região em .  |
|  Parâmetro inválido  |  O nome de utilizador do {0} Windows não pode ser mais do que caracteres {1}longos, terminar com um período (.) ou conter os seguintes caracteres: .  |
|  Parâmetro inválido  |  O nome do computador {0} windows não pode ser mais do que {1}caracteres longos, ser inteiramente numérico, ou conter os seguintes caracteres: .  |
|  Recursos Dependentes de Movimento Desaparecidos  |  O pedido de recursos de deslocação não contém todos os recursos dependentes. Verifique os detalhes de erro dos ids de recurso em falta.  |
|  MoveResourcesHaveInvalidState  |  O pedido de Recursos Move contém VMs que estão associados a contas de armazenamento inválidas. Verifique os detalhes destes ids de recursos e nomes de contas de armazenamento referenciados.  |
|  Operações de Movimentação de Recursos Comportados  |  O pedido de recursos de deslocação contém recursos para os quais está pendente uma operação. Por favor, verifique minuciosamente os detalhes destes problemas de recursos. Tente novamente a sua operação uma vez concluídas as operações pendentes.  |
|  MoveResourcesNotFound  |  O pedido de recursos em movimento contém recursos que não podem ser encontrados. Por favor, verifique minuciosamente os detalhes destes problemas de recursos.  |
|  NetworkingInternalOperationError  |  Erro de atribuição de rede desconhecido.  |
|  NetworkingInternalOperationError  |  Erro de atribuição de rede desconhecido  |
|  NetworkingInternalOperationError  |  Ocorreu um erro interno no processamento do perfil da rede do VM.  |
|  NotFound  |  O Conjunto {0} de Disponibilidade não pode ser encontrado.  |
|  NotFound  |  A "Máquina{0}Virtual fonte" especificada no pedido não existe neste local Azure.  |
|  NotFound  |  Inquilino com {0} identidade não encontrada.  |
|  NotFound  |  A {0} Imagem não pode ser encontrada.  |
|  Não Apoiado  |  O tipo {0}de licença é, {1} mas a bolha de imagem não é do local.  |
|  OperaçãoNotallowed  |  O Conjunto {0} de Disponibilidade não pode ser eliminado. Antes de apagar um Conjunto de Disponibilidade, certifique-se de que não contém nenhum VM.  |
|  OperaçãoNotallowed  |  Não é permitida a alteração do conjunto de disponibilidade SKU de 'Alinhado' para 'Classic'.  |
|  OperaçãoNotallowed  |  Não é possível modificar extensões no VM quando o VM não estiver em funcionamento.  |
|  OperaçãoNotallowed  |  A ação Capture é suportada apenas numa Máquina Virtual com discos à base de bolhas. Utilize o recurso 'Image' APIs para criar uma Imagem a partir de uma Máquina Virtual gerida.  |
|  OperaçãoNotallowed  |  O {0} recurso não pode {1} ser criado a partir da Imagem até que a Imagem tenha sido criada com sucesso.  |
|  OperaçãoNotallowed  |  Atualizações para encriptaçãoAs configurações não são permitidas quando o VM é atribuído, Por favor, tente novamente depois de vM ser deallo  |
|  OperaçãoNotallowed  |  A adição de um disco gerido a um VM com discos à base de bolhas não é suportada.  |
|  OperaçãoNotallowed  |  O número máximo de discos de dados autorizados a {0}ser ligados a um VM deste tamanho é de .  |
|  OperaçãoNotallowed  |  A adição de um disco à base de bolha para VM com discos geridos não é suportada.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não{1}é permitida na Imagem ' ' uma vez que a imagem está marcada para eliminação. Só pode voltar a tentar a operação Eliminar (ou esperar que uma em curso esteja concluída).  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não{1}é permitida na VM ' uma vez que o VM é generalizado.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não é{1}permitida como recolha de pontos de restauro ' está marcada para eliminação.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não é{1}permitida na extensão VM ', uma vez que está marcada para eliminação. Só pode voltar a tentar a operação Eliminar (ou esperar que uma em curso esteja concluída).  |
|  OperaçãoNotallowed  |  A{0}operação ' ' ' não{1}é permitida uma vez que{2}as Máquinas Virtuais ' estão a ser aprovisionadas utilizando a Imagem '.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' ' não é{1}permitida uma vez que{2}o Conjunto de Escala de Máquina Virtual ' ' está atualmente a utilizar a Imagem '.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não{1}é permitida na VM ', uma vez que o VM está marcado para eliminação. Só pode voltar a tentar a operação Eliminar (ou esperar que uma em curso esteja concluída).  |
|  OperaçãoNotallowed  |  {0}A operação ' ' não{1}é permitida na VM ', uma vez que o VM está ou está deallo ou marcado para ser deallo.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não{1}é permitida na VM ' uma vez que o VM está em execução. Por favor, desligue explicitamente caso desligue o VM de dentro do sistema operativo convidado.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não{1}é permitida na VM ', uma vez que o VM não é transferido.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não{1}é permitida na VM ' ' uma vez que a VM tem extensão '{2}em estado falhado.  |
|  OperaçãoNotallowed  |  A{0}operação ' ' não{1}é permitida na VM ' uma vez que outra operação está em curso.  |
|  OperaçãoNotallowed  |  A operação{0}' ' requer{1}que a Máquina Virtual ' seja generalizada.  |
|  OperaçãoNotallowed  |  A operação requer que o VM esteja em funcionamento (ou pronto a funcionar).  |
|  OperaçãoNotallowed  |  Não é {0}permitido o disco com {1}tamanho GB, que é menor do que o tamanho GB do disco correspondente em Imagem.  |
|  OperaçãoNotallowed  |  As extensões vm scale{0}set do manipulador ' só podem ser adicionadas no momento da criação do Conjunto de Escala VM.  |
|  OperaçãoNotallowed  |  As extensões do conjunto{0}de escala vM do manipulador ' só podem ser eliminadas no momento da eliminação do Conjunto de Escala VM.  |
|  OperaçãoNotallowed  |  VM{0}' ' já está usando discos geridos.  |
|  OperaçãoNotallowed  |  VM{0}' ' pertence ao conjunto de{1}disponibilidade 'Clássico' '. Por favor, atualize o conjunto de disponibilidade sku 'Alinhado' e, em seguida, tente novamente a Conversão.  |
|  OperaçãoNotallowed  |  VM criado a partir da Imagem não pode ter discos à base de bolhas. Todos os discos têm de ser geridos.  |
|  OperaçãoNotallowed  |  A operação de captura não pode ser concluída porque o VM não é generalizado.  |
|  OperaçãoNotallowed  |  As operações de{0}gestão em VM ' ' são proibidas porque os discos VM estão a ser convertidos para discos geridos.  |
|  OperaçãoNotallowed  |  Uma operação em curso está {0} a {1}mudar o estado de energia da Máquina Virtual para . Por favor, execute a operação {2} depois de algum tempo.  |
|  OperaçãoNotallowed  |  Incapaz de adicionar ou atualizar o VM. O tamanho {0} de VM solicitado pode não estar disponível na unidade de atribuição existente. Leia mais sobre a estratégia https://aka.ms/azure-resizevmde redimensionamento vm em .  |
|  OperaçãoNotallowed  |  Não é possível redimensionar o {0} VM porque o tamanho solicitado não está disponível no cluster onde o conjunto de disponibilidade está atualmente atribuído. Os tamanhos disponíveis {1}são: . Leia mais sobre a estratégia https://aka.ms/azure-resizevmde redimensionamento vm em .  |
|  OperaçãoNotallowed  |  Não é possível redimensionar o {0} VM porque a dimensão solicitada não está disponível no cluster onde o VM está atualmente atribuído. Para redimensionar o {1} seu VM para desalocar (isto é stop operation in the Azure portal) e tentar a operação de redimensionar novamente. Leia mais sobre a estratégia https://aka.ms/azure-resizevmde redimensionamento vm em .  |
|  OSProvisioningClientError  |  O fornecimento de OS falhou para a VM '{0}porque o oss o de hóspedes está atualmente a ser provisionado.  |
|  OSProvisioningClientError  |  O fornecimento de OS{0}para VM ' falhou. Detalhes do {1} erro: Certifique-se de que a imagem está devidamente preparada (generalizada). <ul><li>Instruções para janelas:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  A geração-chave do hospedeiro SSH falhou. Detalhes do {0}erro: . Para resolver este problema, verifique se o agente Linux está corretamente configurado. <ul><li>Pode consultar as instruções em:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  O nome de utilizador especificado para o VM é inválido para esta distribuição linux. Detalhes do {0}erro: .  |
|  OSProvisioningInternalError  |  O fornecimento de OS falhou para a VM '{0}devido a um erro interno.  |
|  OSProvisioningTimedOut  |  O fornecimento de Os{0}vm ' não terminou no tempo atribuído. O VM ainda pode terminar o fornecimento com sucesso. Por favor, verifique o estado de provisionamento mais tarde.  |
|  OSProvisioningTimedOut  |  O fornecimento de Os{0}vm ' não terminou no tempo atribuído. O VM ainda pode terminar o fornecimento com sucesso. Por favor, verifique o estado de provisionamento mais tarde. Além disso, certifique-se de que a imagem está devidamente preparada (generalizada).   <ul><li>Instruções para janelas:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  O fornecimento de Os{0}vm ' não terminou no tempo atribuído. No entanto, o agente convidado vM foi detetado a correr. Isto sugere que o osso hóspede não foi devidamente preparado para ser usado como uma imagem VM (com CreateOption=FromImage). Para resolver este problema, utilize o VHD como está com createOption=Attach ou prepare-o corretamente para utilização como imagem:   <ul><li>Instruções para janelas:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  Pedido de Alocação OverConstrained  |  O tamanho vm necessário não está disponível no local selecionado.  |
|  RecursoUpdateBlockedOnPlatformUpdate  |  O recurso não pode ser atualizado neste momento devido à atualização da plataforma em curso. Tente novamente mais tarde.  |
|  Limitação de Conta de Armazenamento  |  A conta{0}de armazenamento ' não suporta as bolhas de página que são necessárias para criar discos.  |
|  Limitação de Conta de Armazenamento  |  A{0}conta de armazenamento ' ' excedeu a sua quota atribuída.  |
|  ArmazenamentoAccountLocationMismatch  |  Não conseguiu {0}resolver a conta de armazenamento. Certifique-se de que foi criado através do Fornecedor de Recursos de Armazenamento no mesmo local que o recurso computacional.  |
|  ArmazenamentoAccountNotFound  |  Conta {0} de armazenamento não encontrada. Certifique-se de que a conta de armazenamento não é eliminada e pertence à mesma localização Azure que a VM.  |
|  ArmazenamentoAccountNotRecognized  |  Por favor, utilize uma conta de armazenamento gerida pelo Fornecedor de Recursos de Armazenamento. A {0} utilização não é suportada.  |
|  ArmazenamentoAccountOperationInternalError  |  Erro interno ocorreu durante {0}o acesso à conta de armazenamento .  |
|  Emailde Subscrição de Contas de Armazenamento  |  A {0} conta de armazenamento {1}não pertence à subscrição.  |
|  ArmazenamentoAccountTooBusy  |  A conta{0}de armazenamento ' ' está muito ocupada atualmente. Considere usar outra conta.  |
|  ArmazenamentoAccountTypeNotSupported  |  O {0} {1} disco utiliza, que é uma conta de armazenamento Blob. Por favor, tente novamente com a conta de armazenamento de propósito geral.  |
|  ArmazenamentoAccountTypeNotSupported  |  A {0} conta {1} de armazenamento é de tipo. Boot Diagnostics {2} suporta tipos de conta de armazenamento.  <ul><li>Este erro ocorre se utilizar a conta de armazenamento premium para diagnósticos boot. Para mais informações, consulte [Como utilizar diagnósticos](boot-diagnostics.md)de arranque . </li></ul> |
|  SubscriçãoNotAuthorizedForImage  |  A subscrição não está autorizada.  |
|  TargetDiskBlob Já Existe  |  Blob {0} já existe. Por favor, forneça um blob URI diferente{1}para criar um novo disco de dados em branco ' '.  |
|  TargetDiskBlob Já Existe  |  A operação de captura não {0} pode continuar porque a bolha de imagem alvo já existe e a bandeira para substituir as bolhas VHD não está definida. Ou apague a bolha ou coloque a bandeira para substituir bolhas VHD e retentar novamente.  |
|  TargetDiskBlob Já Existe  |  A operação de captura não {0} pode continuar porque a blob de imagem alvo tem um arrendamento ativo.   |
|  TargetDiskBlob Já Existe  |  Blob {0} já existe. Por favor, forneça uma blob{1}URI diferente como alvo para o disco' ' '  |
|  Pedidos de Reafectação TooManyVM  |  Foram recebidos demasiados pedidos de reafectação para vm '{0}ou para os VMs ' no mesmo conjunto de disponibilidade com este VM. Por favor, tente mais tarde.  |
|  VHDSizeInválido  |  O valor de tamanho {0} do{1}disco especificado {2} para o disco ' com blob é inválido. O tamanho do {3} {4}disco deve estar entre e .  |
|  Erro de comunicação vMAgentStatus  |  VM{0}' ' não reportou o estado para o agente ou extensões VM. Verifique se o VM tem um agente VM em execução e pode estabelecer ligações de saída ao armazenamento Azure.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro durante a comunicação com o repositório de artefactos para recuperar detalhes do artefacto VM.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro interno durante a recuperação dos dados do artefacto VM do repositório de artefactos.  |
|  VMExtensionHandlerNonTransientError  |  Handler{0}' ' reportou falha{1}para extensão VM ' com código de erro terminal '{2}' e mensagem de erro: ' '{3}  |
|  VMExtensionManagementInternalError  |  Erro interno ocorreu durante o{0}processamento da extensão VM ' ' ' ' ' ' '  |
|  VMExtensionManagementInternalError  |  Ocorreram vários erros durante a preparação das extensões VM. Consulte a visão da extensão vM para obter mais detalhes.  |
|  VMExtensionProvisioningError  |  A VM relatou uma{0}falha no processamento da extensão. Mensagem de{1}erro: "."  |
|  VMExtensionProvisioningError  |  Várias extensões vm não foram disponibilizadas no VM. Por favor, consulte a vista de extensão VM para mais detalhes.  |
|  VMExtensionProvisioningTimeout  |  O fornecimento da extensão{0}VM ' tem esgotado. A instalação da extensão pode demorar demasiado tempo, ou o estado de extensão não pode ser obtido.  |
|  VMMarketplaceInvalidInputputput  |  A criação de uma máquina virtual a partir de uma imagem não do Marketplace não precisa de informações do Plano, por favor remova a informação do Plano no pedido. O nome {0}do disco OS é .  |
|  VMMarketplaceInvalidInputputput  |  A informação de compra não corresponde. Incapaz de implantar a partir da imagem do Marketplace. O nome {0}do disco OS é .  |
|  VMMarketplaceInvalidInputputput  |  A criação de uma máquina virtual a partir da imagem do Marketplace requer informações de Plano no pedido. O nome {0}do disco OS é .  |
|  VMNotFound  |  O VM{0}' não pode ser encontrado.  |
|  VMRedeploymentFailed  |  A reafectação da{0}VM falhou devido a um erro interno. Por favor, tente mais tarde.  |
|  VMRedeploymentTimedOut  |  A recolocação{0}de VM ' não terminou no tempo atribuído. Pode terminar com sucesso em algum momento. Caso contrário, pode voltar a tentar o pedido.  |
|  VMStartTimedOut  |  VM{0}' não começou no tempo atribuído. O VM ainda pode começar com sucesso. Por favor, verifique o estado de energia mais tarde.  |


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
