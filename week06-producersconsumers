/*
 * This program simulates the producer-consumer problem using POSIX mutex and semaphore.
 */
#include <pthread.h>
#include <semaphore.h>
#include <stdlib.h>
#include <stdio.h>
#define MAX 5 //define the maximum number of items that a consumer/producer consumes/produces
#define BUFFSIZE 5 //buffer size
sem_t empty; //semaphore empty
sem_t full; //semaphore full
int buffer[BUFFSIZE]; 
pthread_mutex_t mutex; //mutex lock
int in = 0; //buffer index for producer
int out = 0; //buffer index for consumer
int item = 0; //number of items

void *producer(void *p) //pointer p is used to passed a value (thread number) to newly created thread
{   
    for(int i = 0; i < MAX; i++) { //a producer produces MAX items
        sem_wait(&empty); //semaphore wait
        pthread_mutex_lock(&mutex); //mutex lock
        buffer[in] = item; //insert the item to the buffer
        item = item + 1;
        //a nummerical value (thread number) is passed the thread using pointer p, 
        //"*((int *)p)" first converts pointer to a int pointer using (int*)p, then use
        // "*" to get the value from the converted pointer
        printf("producer %d: insert item %d at\tbuffer[%d]\n", *((int *)p), buffer[in], in);
        in = (in+1) % BUFFSIZE; //move the index "in" to the beginning if it hits the end of array
        pthread_mutex_unlock(&mutex); //unlock mutex
        sem_post(&full); //semaphore signal
    }
}

//the consumer is almost exactly the same as producer, comments are ingnored here
void *consumer(void *c)
{   
    for(int i = 0; i < MAX; i++) {
        sem_wait(&full);
        pthread_mutex_lock(&mutex);
        int item_removed = buffer[out];
        printf("consumer %d: remove item %d from buffer[%d]\n",*((int *)c), item_removed, out);
        out = (out+1) % BUFFSIZE;
        pthread_mutex_unlock(&mutex);
        sem_post(&empty);
    }
}

int main()
{   
    pthread_t p[5],c[5]; //array p[5] and c[5] are used to label 5 producers and consumers, respectively
    pthread_mutex_init(&mutex, NULL); //initialize mutex to unlocked
    sem_init(&empty, 0, BUFFSIZE); //initialize semaphore empty to value of BUFFSIZE, which is 5
    sem_init(&full,0,0); //initialize semaphore full to zero
    int id;
    int a[5] = {1,2,3,4,5}; //Keep track of the producer and consumer by labeling 5 threads (producers or consumers) as 1,2,3,4,5
    for(int i = 0; i < 5; i++) { //create 5 producers
        //parameters for pthread_create() fucntion: 
        //&p[i]: An opaque, unique identifier for the new thread returned by the subroutine.
        //attr: is used to set thread attributes, NULL for the default values.
        //(void*)producer: actual C routine for the new thread
        //(void *)&a[i]): A single argument that may be passed to start_routine. 
        //It must be passed by reference as a pointer cast of type void.
        pthread_create(&p[i], NULL, (void *)producer, (void *)&a[i]);
    }
    for(int i = 0; i < 5; i++) { //same as above
        pthread_create(&c[i], NULL, (void *)consumer, (void *)&a[i]);
    }
    for(int i = 0; i < 5; i++) { //main thread waits for the newly created thread to finish
        pthread_join(p[i], NULL);
    }
    for(int i = 0; i < 5; i++) { //same as above
        pthread_join(c[i], NULL);
    }
    //clean up the memory
    pthread_mutex_destroy(&mutex);
    sem_destroy(&empty);
    sem_destroy(&full);
    return 0;   
}
