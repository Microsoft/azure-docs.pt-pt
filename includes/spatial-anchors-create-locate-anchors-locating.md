## <a name="locating-a-cloud-spatial-anchor"></a>Localizar uma âncora de geográficos de cloud

Para localizar as âncoras da cloud espaciais, terá de saber os seus identificadores. Os IDs de âncora podem ser armazenado no seu serviço de back-end do aplicativo s e acessível a todos os dispositivos que podem autenticar corretamente ao mesmo. Para obter um exemplo de uma veja [Tutorial: Partilhar as âncoras geográficos em todos os dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Criar uma instância de um objeto de AnchorLocateCriteria, defina os identificadores que está à procura e invoca o método de CreateWatcher na sessão, fornecendo seu AnchorLocateCriteria.
