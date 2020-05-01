# Carla-Driving-Parallel-Parking
The Python API file simulates a vehicle driving in Town5 and parallel park the vehicle by wapointing.


##############################################################################
##############################################################################
#
# -----------------------------ahamedtufail97--------------------------------#
# ------------ CARLA - TOWN -05 DRIVING BY WAYPOINT FOLLOWING ---------------#
# ------------ THE SPECIFIC POOINTS ARE CHOSEN AND INDIVIDUAL ---------------#
# -------------- DRIVING COMMANDS ARE GIVEN TO THE VEHICLE AT ---------------#
# ------------------------- EACH CO-ORDINATE POINT --------------------------#
#
##############################################################################
##############################################################################

import rospy
import carla
import time
import random
from carla_msgs.msg import CarlaWorldInfo



invert_steering_point = -121.9

parked_locations = [carla.Transform(carla.Location( y=-41.3,x=-118.2, z=0.05), carla.Rotation(yaw=270)),
                    carla.Transform(carla.Location( y=-22.4,x=-118.2, z=0.05), carla.Rotation(yaw=270)),
                    carla.Transform(carla.Location( y=-48.4,x=-118.2, z=0.05), carla.Rotation(yaw=270)),
                    carla.Transform(carla.Location( y=-16.4,x=-118.2, z=0.05), carla.Rotation(yaw=270))]
class CarlaParkVehicle(object):
    """
    class responsable of:
    -spawning 3 vehicles of which one ego
    -interact with ROS and carla server
    -destroy the created objects
    -execute the parking manoeuvre
    """
    def __init__(self):

        """
        construct object CarlaParkVehicle with server connection and
        ros node initiation
        """
        rospy.init_node('park_vehicle', anonymous=True)
        client = carla.Client('localhost', 2000)
        client.set_timeout(2.0)
        self.world = client.get_world()
        self.actor_list = []
        blueprint_library = self.world.get_blueprint_library()

        #create ego vehicle
        bp = random.choice(blueprint_library.filter('vehicle.audi.etron'))
        init_pos = carla.Transform(carla.Location(x=35.3, y=-38.6, z=0.05), carla.Rotation(yaw=272))
        self.vehicle = self.world.spawn_actor(bp, init_pos)
        self.actor_list.append(self.vehicle)
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))
        print("ALL SET ...READYYYY")

        #create 2 parked vehicles
        for pos in parked_locations:
            v = self.world.spawn_actor(bp, pos)
            self.actor_list.append(v)

        rospy.loginfo('created %s' % self.vehicle.type_id)

              
        
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))
            if self.vehicle.get_location().y < -184.9:
                break
        print("THIS CORONA MANIA IS KILLING ME...I WANNA GO FOR A RIDEEE ON THE HIGHWAY...TURNN RIGHTT")    
        while self.vehicle.get_location().y < -184.9:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.2, brake=0.0, steer = 0.4))
            if  (self.vehicle.get_transform().rotation.yaw) > 0 :
                break
        
        #### go straight and turn right #### 
             
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))
            if self.vehicle.get_location().x > 99:
                break
        print("OOPS!!!")        
        while self.vehicle.get_location().x > 99:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.35, brake=0.0, steer = 0.0265))
            if  (self.vehicle.get_transform().rotation.yaw) > 90:
                break
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.45, brake=0.0))     
        print("VOICE COMMAND : PLAY MUSIC")
        

        ####proceed to next right and turn right####
            
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.45, brake=0.0))
            if self.vehicle.get_location().y > 98:
                break
        while self.vehicle.get_location().y > 98:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.35, brake=0.0, steer = 0.0278))
            if (self.vehicle.get_transform().rotation.yaw) < 0: 
                break
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))  
        print("OHH SHOOTT!!I JUST REMEMBERED.ELON MUSK HAS SHOWCASED THE CYBERTRUCK IN OUR TOWN...LETS GO CHECK IT OUT...") 
        
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))
            if self.vehicle.get_location().x < 40:
                break     
        while self.vehicle.get_location().x < 40:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0))
            time.sleep(1)         
            if self.vehicle.get_location().x < 39:
                self.vehicle.apply_control(carla.VehicleControl(throttle=0.28, brake=0.0))
                break
        print("TURN RIGHT")    
        ####turn right to enter city#####
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.28, brake=0.0))
            if self.vehicle.get_location().x < 35:
                break     
        while self.vehicle.get_location().x < 35:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.2, brake=0.0, steer = 0.5))
            if abs (self.vehicle.get_transform().rotation.yaw) < 90:
                self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0)) 
                break          
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0))
        time.sleep(0.1)
        print("GO STRAIGHT")
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, steer=0.0, brake=0.0)) 
    
        #####TRAFFIC LIGHT####

        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.3, brake=0.0))
            if self.vehicle.get_location().y < 15.4:
                break
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0))
               
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.3, brake=0.0))
            if self.vehicle.get_location().y < -1.5:
                break
        while self.vehicle.get_location().y < -1.5:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.2, brake=0.0, steer = -0.6))  
            if (self.vehicle.get_transform().rotation.yaw) > 45 : 
                self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, steer=0.0, brake=1.0))
                break  
        
        print ("IM PRETTY SURE ITS SOMEWHERE IN HERE")
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, steer=0.0))
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.3, brake=0.0))
            if self.vehicle.get_location().x < -110:
                break
        print("I SEE IT THE RED CARPET BY THE LEFT BUILDING")
        print("TURN RIGHT THERE IS PARKING THERE!")
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0)) 
        time.sleep(2.0)
            
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.3, brake=0.0))
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))
            if self.vehicle.get_location().x < -118:
                break
            
        while self.vehicle.get_location().x < -118:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.2, brake=0.0, steer = 0.5))  
            if abs (self.vehicle.get_transform().rotation.yaw) < 90 : 
                self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, steer=0.0, brake=1.0))
                break
                  
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0))
        time.sleep(0.3)
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))
        

        
        """
        function to move the ego vehicle into 'start parking' position
        """
        while self.vehicle.get_location().y > -35 and self.vehicle.get_location().x < -121:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.4, brake=0.0))
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0))
        print("PARKING SPOT FOUND")
        time.sleep(1)

    
        
        ###function enables the ego vehicle to enter the parking spot 
        
        while True:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.3, steer=0.6, brake=0.0, reverse=True))
            time.sleep(0.1)
            if self.vehicle.get_location().x > invert_steering_point:
                break
        while self.vehicle.get_location().x > invert_steering_point:
            self.vehicle.apply_control(carla.VehicleControl(throttle=0.3, steer=-0.6, brake=0.0, reverse=True))
            time.sleep(0.1)
            if abs(self.vehicle.get_transform().rotation.yaw) < 92 :
                self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, steer=0.0, brake=1.0, reverse=True))
                break
        print("THAT'S ENOUGH")    
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0))
        time.sleep(0.5)
        print("STOP")
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.3, steer=0.0, brake=0.0, reverse=False))
        time.sleep(1)
        self.vehicle.apply_control(carla.VehicleControl(throttle=0.0, brake=1.0))
        time.sleep(0.5)
        print("GET OUT")
        time.sleep(10)
        return

    def destroy(self):
        """
        destroy all the actors
        """
        print('destroying actors')
        for actor in self.actor_list:
            actor.destroy()
        print('done.')


    def run(self):
        """
        main loop
        """
        # wait for ros-bridge to set up CARLA world
        rospy.loginfo("Waiting for CARLA world (topic: /carla/world_info)...")
        try:
            rospy.wait_for_message("/carla/world_info", CarlaWorldInfo, timeout=10.0)
        except rospy.ROSException as e:
            rospy.logerr("Timeout while waiting for world info!")
            raise e
        rospy.loginfo("CARLA world available. Spawn ego vehicle...")

        
            

        print("press CTRL+C to terminate the node")
        try:
            rospy.spin()
        except rospy.ROSInterruptException:
            pass



# ==============================================================================
# -- main() --------------------------------------------------------------------
# ==============================================================================


def main():
    """
    Main function
    """
    ego_vehicle = CarlaParkVehicle()
    try:
        ego_vehicle.run()
    finally:
        if ego_vehicle is not None:
            ego_vehicle.destroy()


if __name__ == '__main__':
    try:
        main()
    except rospy.ROSInterruptException:
        pass     
