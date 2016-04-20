# IO

## Disk

### Cost model for Disk Block Transfer

* seek time: move the read/write heads to the appropriate cylinder
* rotational latency: wait until the desired sectors spin to the read/write heads
* transfer time: wait while the desired sectors spin pas the read/write heads

### Rotational Latency

rotational latency depends on the rotational speed of the disk

* if the disk spins at w rotations per second: 0 <= t_rot <= 1/w
* expected rotational latency: E(t_rot)=1/(2w)

### Transfer Time

Transfer time depends on the rotational speed and on the amount of data transferred

* if k sectors are to be transferred and there are T sectors per track: t_transfer = k/(Tw)

### Seek Time

seek time depends on the speed of the arm on which the read/write heads are mounted

* if k is the required seek distance, C is the total number of cylinders, t_maxseek is the time required to move the read/write heads from the innermost cylinder to the outermost cylinder: t_seek(k)=t_maxseek*k/C

### Performance Characteristics

* Larger transfers are more efficient than smaller ones
* sequential I/O is faster than non-sequential

## Disk Head Scheduling

reduce seek times by controlling the oder in which requests are serviced

* First-Come-First-Serve: fair and simple, no optimization
* Shortest-Seek-Time-First: seek time are reduced, but may starve
* Elevator Algorithm: disk head moves in one direction until there are no more requests in front of it, then reverse the direction