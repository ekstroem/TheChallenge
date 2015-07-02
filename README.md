# TheChallenge

## Mål
Man skal - kun ved hjælp af transport med S-tog i hovedstadsområdet - se, hvor hurtig man
kan transportere sig rundt langs S-togsnettet. Kravene er, at man 

1. stopper på alle stationer (men behøver ikke at forlade toget) mindst
en gang.
2. kan starte på en vilkårlig station og kan ende på en vilkårlig station
3. Høvelte skal _ikke_ tages med på listen af stationer, der skal besøges.

Altså et traveling salesmanproblem, hvor man
 * ikke har en begrænsning på kun at besøge hver by en gang.
 * skal tage højde for ventetider mellem togene


Eksempel på data kan findes [her](data/tog.csv), hvilket er
oplysninger fra de hvide køretider hentet 1. juli 2015. Bemærk, at
linje Bx ikke er med i de pågældende data


```R
# Read data
indata <- read.csv2("data/tog.csv", header=FALSE, col.names=c("station",
"tid"), as.is=TRUE)

# Simple data fixing
convertData <- function(o) {
no <- NROW(o)
difft <- diff(o$tid)
    difft[!is.na(difft) & difft<0] <- difft[!is.na(difft) & difft<0] + 60
	    res <- data.frame(from=o$station[-no], to=o$station[-1],
		tid=difft, weight=difft)
		res[!is.na(res$tid),]
		}

tog <- convertData(indata)


library(igraph)

# This object contains a graph data frame with the columns
# from, to, dist and weight. Dist and weight are identical
# And refer to the distance (in minutes) between neighboring stations
network <- graph.data.frame(tog, directed=FALSE)

# This matrix contains an 84x84 matrix of neighboring stations
neighbormatrix <- as.matrix(get.adjacency(network))

# And this matrix contains minimum dtstance (in minutes) between
# all pairs of stations
distancematrix <- shortest.paths(network)

```

![Network graph]("https://cdn.rawgit.com/ekstroem/TheChallenge/master/pics/graph.svg")
