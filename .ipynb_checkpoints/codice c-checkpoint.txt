#include<stdio.h>
#include<stdlib.h>
#include<math.h>

float *myrandgauss(int seme, int enne, float mm, float vv){
        int i; 
	float *y;                             
	y=(float *)malloc(enne*sizeof(float));
	float x1,x2,gam;
	
	srand(seme); 
	for(i=0;i<enne;i++){
	    x1=(float) rand()/RAND_MAX;
	    if(x1>0){
	       x2=(float) rand()/RAND_MAX;
	       gam=sqrt(-2.*log(x1))*sin(2.*M_PI*x2);
	    }
	    y[i]=mm+(float)sqrt(vv)*gam;
	}
	
	return y;
}


int main(){

        int i,t;

	int T;
	printf("inserisci la lunghezza della serie temporale \n");
	scanf("%d",&T);
	
	float kappa;
	printf("inserisci il valore di kappa \n");
	scanf("%f",&kappa);

	float x0;
	printf("inserisci il valore di x0 \n");
	scanf("%f",&x0);
	
	int seed;
	printf("inserisci il seme della simulazione \n");
	scanf("%d",&seed);
	
	int enne;
	printf("inserisci il valore di enne \n");
	scanf("%d",&enne);
	
	float dt;
	dt=(float)1/enne;
	
	int N;
	N=T*enne;
	
	float *x;
	x=(float *)malloc(N*sizeof(float));
	float *Z;
	Z=(float *)malloc(N*sizeof(float));
	
	float *pd;
	pd=myrandgauss(seed,N,0.,2.*dt);
	for(i=0;i<N;i++){
	   Z[i]=*(pd+i);
	}

	/*Eulero all'ordine piu basso*/
	x[0]=x0;
	for(i=0;i<N;i++){
	   if(x[i]<0.){
	        x[i+1]=x[i]+kappa*dt+Z[i];
		}else{
	        x[i+1]=x[i]-kappa*dt+Z[i];
		 }
	}
	
	float media,varianza;
	media=0;
	varianza=0;
	for(i=0;i<N;i++){
	   media=media+x[i];
	   varianza=varianza+pow(x[i],2.);
	}
	media=(float)media/N;
	varianza=(float)varianza/N-pow(media,2.);
	printf("La media e': %f \n",media);
	printf("La varianza e': %f \n",varianza);
	


        FILE *fp;
	fp=fopen("numeri_RISKEN.dat","w");
	for(i=0;i<N;i++){
	   fprintf(fp,"%d %f \n",i,x[i]);
	}
	fclose(fp);


        int num;
	float *y;
	y=(float *)malloc(T*sizeof(float));
	num=0;
	for(i=0;i<N;i=i+enne){
	    num=num+1;
	    y[num]=x[i];
	}
	printf("%d \n",num);
	float mediay,varianzay;
	mediay=0;
	varianzay=0;
	for(t=0;t<T;t++){
	   mediay=mediay+y[t];
	   varianzay=varianzay+pow(y[t],2.);
	}
	mediay=(float)mediay/T;
	varianzay=(float)varianzay/T-pow(mediay,2.);
	printf("La media di y e': %f \n",mediay);
	printf("La varianza di y e': %f \n",varianzay);


}