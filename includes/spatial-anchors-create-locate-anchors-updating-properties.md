## <a name="updating-properties-on-an-existing-cloud-spatial-anchor"></a>A atualizar as propriedades numa âncora de geográficos cloud existente

Para atualizar as propriedades numa âncora, use o método UpdateAnchorPropertiesAsync. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, vamos utilizar um modelo de simultaneidade otimista. Isto significa que a primeira operação de escrita ganhará.  Todas as outras escritas obterá um erro de "Simultaneidade": uma atualização das propriedades seria necessário antes de tentar novamente.
