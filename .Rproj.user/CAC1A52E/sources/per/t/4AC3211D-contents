library(FactoMineR)
library(factoextra)

lines <- readLines("pays.txt", encoding = "latin1")

# Suppression de l'en-tête initial contenant des espaces incohérents,
# afin d'assurer une lecture correcte des données et de standardiser les noms des variables et des individus
lines_data <- lines[-1]

# Remplace l'espace par "_" pour que le nom soit lu comme un seul mot
lines_data <- gsub("Pays Bas", "Pays_Bas", lines_data)
lines_data <- gsub("Royaume Uni", "Royaume_Uni", lines_data)

# Remplace les espaces multiples par un seul espace pour uniformiser le séparateur
lines_clean <- gsub(" +", " ", lines_data)

# Importation des données et définition des noms des variables
data <- read.table(text = lines_clean, header = FALSE, row.names = 1)
names_vector <- c("esp_vie_F", "mort_inf", "activF", "pct_chom", "pnb_hb", "pct_education", "pct_sante")
                colnames(data) <- names_vector
                
                
# Création d'une variable supplémentaire pour regrouper les pays par zone géographique              
                data$Zone <- with(data, ifelse(rownames(data) %in% c("Allemagne","Belgique","France","Irlande","Luxembourg","Pays_Bas","Royaume_Uni"), "Ouest",
                                        ifelse(rownames(data) %in% c("Danemark","Finlande","Norvège","Suède"), "Nord",
                                        ifelse(rownames(data) %in% c("Espagne","Italie","Portugal","Grèce","Chypre","Malte"), "Sud",
                                        ifelse(rownames(data) %in% c("Autriche","Suisse","Pologne","Tchéquie","Slovaquie","Hongrie","Slovénie"), "Centre",
                                                                    "Est")))))              
                                               
View(data)

# 1. Vérification de la structure des données
str(data)

# 2. Résumé statistique (Moyenne, Min, Max, etc.)
summary(data)

# 3. Vérification des premières lignes
head(data)


# Standardisation des variables et détection visuelle des outliers
data_numeric <- data[, 1:7]
boxplot(scale(data_numeric), # On utilise scale() pour ramener toutes les variables à la même unité
        main = "Détection des outliers",
        las = 2,
        col = "blue")

# Standardisation des données : permet de comparer des variables ayant des unités différentes
data_scaled <- scale(data_numeric)



# Analyse en composantes principales
res.pca <- PCA(data, quali.sup = 8, graph = FALSE)

# Visualisation des variables selon leur contribution et leur qualité de représentation
fviz_pca_var(res.pca, col.var = "cos2", 
             gradient.cols = c("#0057B7", "#FF00A8", "#7F00FF"),
             repel = TRUE)


# Classification des individus à partir des résultats de l'ACP (méthode HCPC)
res.hcpc <- HCPC(res.pca, nb.clust = 3, graph = FALSE)

# Affichage des groupes obtenus par la classification
fviz_cluster(res.hcpc, 
             repel = TRUE, 
             show.clust.cent = TRUE, 
             palette = c("blue", "hotpink", "black"), 
             ggtheme = theme_minimal(), 
             main = "Les 3 grands blocs de l'Europe")

#Dendrogram
plot(res.hcpc, choice = "tree")


#K_mean
# Pour obtenir toujours les mêmes résultats (reproductibilité)
set.seed(123)

# Lancer l'algorithme pour créer 3 groupes (clusters)
res.km <- kmeans(data_scaled, centers = 3, nstart = 25)

print(res.km$cluster)

# Compter le nombre de pays dans chaque groupe
table(res.km$cluster)

# Dessiner le graphique pour voir les groupes en couleurs
library(factoextra)
fviz_cluster(res.km, data = data_scaled,
             palette = c("blue", "hotpink", "black"), 
             ggtheme = theme_minimal(),
             main = "K-means Clustering (k=3)")

#silhouette
library(cluster)
sil <- silhouette(res.km$cluster, dist(data_scaled))
fviz_silhouette(sil)


fviz_pca_ind(res.pca,
             habillage = "Zone",
             addEllipses = TRUE,
             ellipse.level = 0.6, # Réduction de la taille des ellipses pour mieux séparer les groupes
             alpha.ind = 0.7,  # Augmentation de la transparence des points pour améliorer la lisibilité du graphique
             repel = TRUE,
             palette = c("blue", "hotpink", "red", "black", "purple"))
