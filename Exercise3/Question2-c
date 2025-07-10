
#include <pthread.h>
#include <time.h>
#include <math.h>
#include <stdio.h>
#include <unistd.h>

typedef struct {
    double latitude;
    double longitude;
    double altitude;
    double roll;
    double pitch;
    double yaw;
    struct timespec timestamp;
    pthread_mutex_t lock;
} NavState;

NavState nav_state = {.lock = PTHREAD_MUTEX_INITIALIZER};

void* writer_thread(void* arg) {
    double t = 0;
    while (t <= 180) {
        pthread_mutex_lock(&nav_state.lock);
        clock_gettime(CLOCK_REALTIME, &nav_state.timestamp);

        nav_state.latitude = 0.01 * t;
        nav_state.longitude = 0.02 * t;
        nav_state.altitude = 0.025 * t;
        nav_state.roll = sin(t);
        nav_state.pitch = cos(t * t);
        nav_state.yaw = cos(t);

        pthread_mutex_unlock(&nav_state.lock);
        t += 1;
        sleep(1); // 1 Hz rate update
    }
    return NULL;
}

void* reader_thread(void* arg) {
    for (int i = 0; i < 18; i++) {
        pthread_mutex_lock(&nav_state.lock);
        printf("Time: %ld.%09ld | Lat: %.2f, Lon: %.2f, Alt: %.2f | Roll: %.2f, Pitch: %.2f, Yaw: %.2f\n",
               nav_state.timestamp.tv_sec, nav_state.timestamp.tv_nsec,
               nav_state.latitude, nav_state.longitude, nav_state.altitude,
               nav_state.roll, nav_state.pitch, nav_state.yaw);
        pthread_mutex_unlock(&nav_state.lock);
        sleep(10); // 0.1 Hz rate read
    }
    return NULL;
}

int main() {
    pthread_t writer, reader;
    pthread_create(&writer, NULL, writer_thread, NULL);
    pthread_create(&reader, NULL, reader_thread, NULL);
    pthread_join(writer, NULL);
    pthread_join(reader, NULL);
    return 0;
}
